# #22 - Non-allocating docs are a bit unclear [Closed]

> Username: vinniefalco  
> Created at: 2017-06-10 22:25:16 UTC  
> Updated at: 2017-06-11 12:41:18 UTC  
> Closed at: 2017-06-11 12:36:08 UTC  
> Url: https://github.com/boostorg/lexical_cast/issues/22  

I would like to make use of lexical_cast into a caller provided buffer, to avoid memory allocation. The documentation implies that conversion into `boost::array<char, ...>` targets will be null terminated but it is not stated explicitly.  
  
Even so, is there a way to convert to a non-null terminated buffer, and receive the size of the converted result? I would like to avoid a call to char_traits::length.

---

## Comment 1

> Username: apolukhin  
> Created at: 2017-06-11 12:00:43 UTC  
> Url: https://github.com/boostorg/lexical_cast/issues/22#issuecomment-307624782  

Sorry,  there's no way to avoid call to `char_traits::length`.  
  
A few more bad news: lexical_cast uses std::locale and will always use it, because dropping it is a breaking change. std::locale is disaster on almost all the platforms: it uses atomics, checks environment variables,  allocates memory, throws and catches exceptions...  
  
So, if you need an extremely good performance in that particular case, try to use parser from Boost.Spirit as it does not allocate memory and does not use std::locale OR use std::to_chars/std::from_chars from C++17.

---

## Comment 2

> Username: vinniefalco  
> Created at: 2017-06-11 12:13:09 UTC  
> Updated at: 2017-06-11 12:17:19 UTC  
> Url: https://github.com/boostorg/lexical_cast/issues/22#issuecomment-307625373  

LOL wow!!! That's good to know. This is what I built with it  
```  
/** A helper for creating field values.  
  
    This is used as a function parameter type to allow callers  
    notational convenience: objects other than strings may be  
    passed in contexts where a string is expected. The conversion  
    to string is made using facilities in Boost.Conversion,  
    which rely largely on the availability of `operator<<` for  
    the type in question. The algorithm does its best to avoid  
    memory allocation, using an internal static buffer when  
    possible.  
*/  
class field_value  
{  
    boost::array<char, 128> buf_;  
    std::string s_;  
    string_view sv_;  
  
    template<class T>  
    void  
    assign(T const& t, std::true_type)  
    {  
        sv_ = t;  
    }  
  
    template<class T>  
    void  
    assign(T const& t, std::false_type)  
    {  
        // Do our best to avoid allocating  
        if(boost::conversion::try_lexical_convert(t, buf_))  
        {  
            sv_ = string_view{&buf_[0]};  
            return;  
        }  
        // If all else fails, allocate  
        s_ = boost::lexical_cast<std::string>(t);  
        sv_ = {s_.data(), s_.size()};  
    }  
  
public:  
    /** Constructor  
  
        This function constructs a string from the specified  
        parameter.  
          
        If @ref string_view is constructible from `T` then  
        the complexity is O(1), and no dynamic allocation  
        takes place.  
  
        Otherwise, the argument is converted to a string  
        as if by a call to `boost::lexical_cast<std::string>(t)`.  
        An attempt is made to store this string in a reasonably  
        sized buffer inside the class, to avoid dynamic allocation.  
        If the argument could not be converted given the space  
        of the class buffer, a final attempt is made to convert  
        the argument to a `std::string`. This attempt may cause  
        a memory allocation.  
    */  
    template<class T>  
    field_value(T const& t);  
  
    /// Conversion to @ref string_view for the converted argument.  
    string_view const  
    str() const  
    {  
        return sv_;  
    }  
  
    /// Conversion to @ref string_view operator  
    operator string_view const() const  
    {  
        return sv_;  
    }  
};  
  
template<class T>  
field_value::  
field_value(T const& t)  
{  
    assign(t, std::is_convertible<T, string_view>{});  
}  
```  
  
The idea is you have a function signature like this:  
```  
void insert(string_view name, field_value const& value);  
```  
  
And now you can write `insert("Content-Length", 1024)` for example, and whatever you pass for the 2nd parameter gets converted to a string efficiently.  
  
I am only using this to convert to strings, never to parse strings into integers.

---

## Comment 3

> Username: apolukhin  
> Created at: 2017-06-11 12:36:08 UTC  
> Url: https://github.com/boostorg/lexical_cast/issues/22#issuecomment-307626485  

Looks good.  
  
If you wish you could replace the `s_ = boost::lexical_cast<std::string>(t);` with try_lexical_convert and throw your own exceptions instead of bad_lexical_cast

---

## Comment 4

> Username: vinniefalco  
> Created at: 2017-06-11 12:38:18 UTC  
> Url: https://github.com/boostorg/lexical_cast/issues/22#issuecomment-307626610  

But you said its going to set up the locale, allocate memory, and do a bunch of nasty things. So is this technique going to do what I want or not?

---

## Comment 5

> Username: apolukhin  
> Created at: 2017-06-11 12:40:48 UTC  
> Url: https://github.com/boostorg/lexical_cast/issues/22#issuecomment-307626725  

This will work faster than std::ostringstream and more functional than Spirit. This is the best you can get so far

---

## Comment 6

> Username: vinniefalco  
> Created at: 2017-06-11 12:41:18 UTC  
> Url: https://github.com/boostorg/lexical_cast/issues/22#issuecomment-307626746  

In that case, I will keep it - thanks!
