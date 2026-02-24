# #39 - string_view constructor isn't constexpr [Closed]

> Username: akrzemi1  
> Created at: 2018-01-25 17:26:34 UTC  
> Updated at: 2022-05-02 22:46:31 UTC  
> Closed at: 2022-05-02 22:46:31 UTC  
> Url: https://github.com/boostorg/utility/issues/39  

`boost::string_view` could be a good choice for compilers that do not support `constexpr` `string_view` yet. But with `boost::string_view` one cannot create constexpr globals either because the converting constructor isn't constexpr. It calls `char_traits::length`, and the latter is not declared constexpr. Could the definition of the constructor be fixed to use a custom implementation of `length`, at least for type `char`?  
  
See the example of what I describe: https://wandbox.org/permlink/LwJnxVrsHyY1V156

---

## Comment 1

> Username: mclow  
> Created at: 2018-01-25 17:47:29 UTC  
> Url: https://github.com/boostorg/utility/issues/39#issuecomment-360544594  

You can use your own `char_traits` and declare the length method constexpr.

---

## Comment 2

> Username: akrzemi1  
> Created at: 2018-01-25 23:00:41 UTC  
> Url: https://github.com/boostorg/utility/issues/39#issuecomment-360629379  

Looking at the declaration of `operator<<`:  
  
```c++  
template<class charT, class traits>  
  inline std::basic_ostream<charT, traits>&  
  operator<<(std::basic_ostream<charT, traits>& os,  
             const basic_string_view<charT,traits>& str);  
```  
  
I would also have to provide custom `ostream` specializations, and could not use my `string_ref` with `std::cout`.

---

## Comment 3

> Username: mclow  
> Created at: 2018-09-14 17:08:28 UTC  
> Url: https://github.com/boostorg/utility/issues/39#issuecomment-421423668  

I would really rather not do this, because the user expects `traits::length` to be called.  
You could write (untested code):  
  
    constexpr string_view make_string_view(const char *s)  
    { return string_view(s, my_length(s)); }  
  
and call that instead of using the constructor directly.

---

## Comment 4

> Username: pdimov  
> Created at: 2018-12-18 22:52:12 UTC  
> Url: https://github.com/boostorg/utility/issues/39#issuecomment-448401886  

The user can't expect `std::char_traits<char>::length` to be called because it's unobservable.

---

## Comment 5

> Username: fsmoke  
> Created at: 2022-02-12 16:08:20 UTC  
> Url: https://github.com/boostorg/utility/issues/39#issuecomment-1037272424  

besides std::string_view ctors are constexpr and it'seems len traits also constexpr  
cos i can write like this   
  
constexpr std::string_view sv = "Hello";   
  
without any compiler errors  
  
but in this expression  
constexpr boost::string_view sv = "Hello";    
i've got compiler errors

---

## Comment 6

> Username: Lastique  
> Created at: 2022-05-02 22:46:31 UTC  
> Url: https://github.com/boostorg/utility/issues/39#issuecomment-1115441545  

`boost::string_view` constructor [is constexpr now](https://github.com/boostorg/utility/blob/0106ffda5f9d27ab0cb3685a1cc9c234fc78ecd8/include/boost/utility/string_view.hpp#L106-L107), and as long as `std::char_traits::length()` is constexpr, it [works](https://gcc.godbolt.org/z/sWxr3Mbb8) fine.
