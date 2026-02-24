# #212 Variable templates are somewhat unusable in GCC 5.1 (bug 65719) [Merged]

> Username: glenfe  
> Created at: 2018-02-09 13:01:19 UTC  
> Updated at: 2018-02-10 11:59:14 UTC  
> Merged at: 2018-02-10 11:59:14 UTC  
> Closed at: 2018-02-10 11:59:14 UTC  
> Url: https://github.com/boostorg/config/pull/212  

The are only two uses of BOOST_NO_CXX14_VARIABLE_TEMPLATES in Boost right now: Boost.Align (for alignment_of only) and Boost.TypeTraits (for the detection idiom toolkit only), so this should have minimal impact.

---

## Comment 1

> Username: jzmaddock  
> Created_at: 2018-02-09 18:18:56 UTC  
> Url: https://github.com/boostorg/config/pull/212#issuecomment-364514933  

Sorry for being dumb, but what have variadic templates got to do with that bug?

---

## Comment 2

> Username: glenfe  
> Created_at: 2018-02-09 21:51:50 UTC  
> Updated_at: 2018-02-09 22:00:22 UTC  
> Url: https://github.com/boostorg/config/pull/212#issuecomment-364579605  

John, _variable_ templates, not _variadic_ templates.  e.g. These:  
```cpp  
template<class T>  
constexpr std::size_t alignment_of_v = alignment_of<T>::value;  
```  
With GCC 5.1 even correct usage results in the linker errors you're observing with the unit tests where the variable templates `is_detected_v`, `is_detected_exact_v`, and `is_detected_convertible_v` are used.

---

## Comment 3

> Username: jzmaddock  
> Created_at: 2018-02-10 11:59:07 UTC  
> Url: https://github.com/boostorg/config/pull/212#issuecomment-364646653  

>John, variable templates, not variadic templates  
  
Well I did say that I was probably being dumb!  Merging now...

---
