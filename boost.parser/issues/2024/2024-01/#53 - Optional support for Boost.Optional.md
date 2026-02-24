# #53 - Optional support for Boost.Optional? [Closed]

> Username: akrzemi1  
> Created at: 2024-01-07 19:43:55 UTC  
> Updated at: 2024-01-13 03:53:01 UTC  
> Closed at: 2024-01-08 03:23:29 UTC  
> Url: https://github.com/boostorg/parser/issues/53  

I have a game parser that uses Boost.Spirit X3 into my own data structure. My data structure is a combination of aggregates, `std::vector` and `boost::optional`. Just mechanically migrating to Boost.Parser doesn't work, because Boost.Parser recognizes `std::optional` but not `boost::optional`.  
  
Is there a possibility to make Boost.Parser recognize `boost::optional`, given that it already recognizes Boost.Hana? I know it goes against one of the goals of the library. Maybe it doesn't have to be `boost::optional` directly, but any type sufficiently optional-like?

---

## Comment 1

> Username: tzlaine  
> Created at: 2024-01-07 20:27:01 UTC  
> Url: https://github.com/boostorg/parser/issues/53#issuecomment-1880162262  

Well, I'll consider it, and I'll leave this issue open.   However, I am not very enthusiastic abou this idea.  If there are enough people like you who feel that this would be useful, I can probably add this support.  I would almost certainly want to add support for the various variants out there too -- it would be too weird to support multiple optionals but only one variant.  
  
In both cases. I would probably require the user to specialize an opt-in `constexpr` `bool`, a la `std::enable_borrowed_range` (see https://en.cppreference.com/w/cpp/ranges/borrowed_range).

---

## Comment 2

> Username: akrzemi1  
> Created at: 2024-01-07 20:32:59 UTC  
> Url: https://github.com/boostorg/parser/issues/53#issuecomment-1880163702  

I understand. An I understand that Boost.Parser as standalone component doesn't need it.   
  
It is the migration from Boost.Spirit to Boost.Parser that becomes bumpy. I cannot just replace one optional for another in my data structure, as other parts of the program depend on it.

---

## Comment 3

> Username: tzlaine  
> Created at: 2024-01-08 03:24:05 UTC  
> Url: https://github.com/boostorg/parser/issues/53#issuecomment-1880334990  

Turns out this was pretty easy, so I just did it.

---

## Comment 4

> Username: akrzemi1  
> Created at: 2024-01-12 20:35:31 UTC  
> Url: https://github.com/boostorg/parser/issues/53#issuecomment-1889916586  

Would you be willing to go a step further with this support and add something like:  
  
```c++  
#ifdef BOOST_OPTIONAL_INCLUDED  
template<typename T>  
constexpr bool boost::parser::enable_optional<boost::optional<T>> = true;  
#endif  
```

---

## Comment 5

> Username: akrzemi1  
> Created at: 2024-01-12 20:43:36 UTC  
> Url: https://github.com/boostorg/parser/issues/53#issuecomment-1889925755  

Why do I need to   
```  
 #include <boost/optional/optional_io.hpp>  
```  
To make my parser compile?  
Why does parser need to print my attribute?

---

## Comment 6

> Username: tzlaine  
> Created at: 2024-01-13 03:51:27 UTC  
> Url: https://github.com/boostorg/parser/issues/53#issuecomment-1890290471  

> Why do I need to  
>   
> ```  
>  #include <boost/optional/optional_io.hpp>  
> ```  
>   
> To make my parser compile? Why does parser need to print my attribute?  
  
Yeah, about that.  `boost::optional` defines an `operator<<` that `static_asserts`, telling you to include optional_io.hpp.  IOW, it advertizes that it implements `op<<`, but then just breaks everything.  This breaks the `op<<` detection logic in `detail/printing.hpp`.  
  
I had a TODO t odocument this, but today I realized I could work around it, by changing the printing logic a bit.  The fix is on develop if you want to try it.

---

## Comment 7

> Username: tzlaine  
> Created at: 2024-01-13 03:53:00 UTC  
> Url: https://github.com/boostorg/parser/issues/53#issuecomment-1890290829  

> Would you be willing to go a step further with this support and add something like:  
>   
> ```c++  
> #ifdef BOOST_OPTIONAL_INCLUDED  
> template<typename T>  
> constexpr bool boost::parser::enable_optional<boost::optional<T>> = true;  
> #endif  
> ```  
  
No, I think it's pretty easy to define that variable specialization, and I don't want to priveledge *this* non-standard optional over all the others, or encourage people to keep using `boost::optional` when `std::optional` is s much more widely used.
