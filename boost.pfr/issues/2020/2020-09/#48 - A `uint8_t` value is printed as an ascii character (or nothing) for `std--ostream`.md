# #48 - A `uint8_t` value is printed as an ascii character (or nothing) for `std::ostream` [Closed]

> Username: hadrielk  
> Created at: 2020-09-28 14:43:36 UTC  
> Updated at: 2020-09-28 17:36:28 UTC  
> Closed at: 2020-09-28 17:32:35 UTC  
> Url: https://github.com/boostorg/pfr/issues/48  

Because `pfr` uses the stream operator for native type printing, something like this:  
  
```  
struct A {  
    uint8_t u8{49};  
};  
```  
will print: `{1}` because the ascii value of decimal 49 happens to be "1". It would be _blank_ if the `uint8_t` happened to be unprintable ascii - like `0`.  
  
And this is because `std::ostream` usually has a specialization for `ostream& operator<<(ostream&, char)`, which will be found  when the `uint8_t` gets implicitly converted to `char` (if it isn't already just a typedef for to begin with), when it's invoked like this in `io.hpp`'s `print_impl()`:  
  
```  
        out << detail::quoted_helper(boost::pfr::detail::sequence_tuple::get<I>(value));  
```  
  
_Usually_ what people do is promote the `uint8_t` to an `int` by doing `+value` or casting; but in `pfr`'s case it might be better to do it some other way.  
  
Also, technically this issue applies to simple `char`, `unsigned char` and `signed char` members of classes/struct too. It's just not safe to output them directly using `<<`, unfortunately. (well... it's not _unsafe_ - but it won't print what one would expect)

---

## Comment 1

> Username: apolukhin  
> Created at: 2020-09-28 17:32:35 UTC  
> Url: https://github.com/boostorg/pfr/issues/48#issuecomment-700177628  

Yep, `uint8_t` is a known pain because it's a typedef. Unfortunately that issues can not be solved at the library level because in the same program there may be places where `char` is meant to be outputted as `char`, and other places, where it meant to be outputted as `int`.  
  
A good option for integral types is `strong typedef`.  
```  
    enum class MyInt8: signed char {};  
    std::ostream& operator<<(std::ostream&, MyInt8);  
```  
Unfortunately, those are not always simple to use

---

## Comment 2

> Username: hadrielk  
> Created at: 2020-09-28 17:36:28 UTC  
> Url: https://github.com/boostorg/pfr/issues/48#issuecomment-700179559  

Yeah, sorry, I didn't mean to sound obnoxious - I figured you knew why already, but I was being verbose in case someone else finds this issue in the future.
