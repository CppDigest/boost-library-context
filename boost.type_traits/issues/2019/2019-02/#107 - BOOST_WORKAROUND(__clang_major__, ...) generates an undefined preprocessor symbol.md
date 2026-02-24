# #107 - BOOST_WORKAROUND(__clang_major__, ...) generates an undefined preprocessor symbol [Open]

> Username: nivekkagicom  
> Created at: 2019-02-06 19:21:13 UTC  
> Updated at: 2021-07-04 16:20:57 UTC  
> Url: https://github.com/boostorg/type_traits/issues/107  

This code in config.hpp:  
```  
#if !defined(BOOST_NO_CXX11_VARIADIC_TEMPLATES) && !BOOST_WORKAROUND(BOOST_GCC, < 40900)\  
      && !BOOST_WORKAROUND(BOOST_MSVC, < 1900) && !BOOST_WORKAROUND(__clang_major__, <= 4)  
#  define BOOST_TT_HAS_ASCCURATE_IS_FUNCTION  
#endif  
```  
Can generate a warning or error as __clang_major___WORKAROUND_GUARD is not defined.

---

## Comment 1

> Username: Hakuhonoo  
> Created at: 2020-02-11 14:16:47 UTC  
> Url: https://github.com/boostorg/type_traits/issues/107#issuecomment-584655830  

Its actually, doing this for me

---

## Comment 2

> Username: chuchusoft  
> Created at: 2020-08-01 06:47:47 UTC  
> Url: https://github.com/boostorg/type_traits/issues/107#issuecomment-667483794  

I also get this problem (using /Wall with clang-cl on Visual Studio 2019 16.6.4)  
  
Also, the #define symbol is incorrectly spelled: BOOST_TT_HAS_ASCCURATE_IS_FUNCTION should probably be BOOST_TT_HAS_ACCURATE_IS_FUNCTION

---

## Comment 3

> Username: pdimov  
> Created at: 2021-07-04 13:00:48 UTC  
> Url: https://github.com/boostorg/type_traits/issues/107#issuecomment-873585434  

This has also been reported in https://github.com/boostorg/config/issues/322.  
  
An additional problem with this check is that `__clang_major__` can be more than 4 on macOS, but Clang can still effectively be 3.x according to the official versioning, as Apple use their own versioning scheme.

---

## Comment 4

> Username: pdimov  
> Created at: 2021-07-04 16:20:57 UTC  
> Url: https://github.com/boostorg/type_traits/issues/107#issuecomment-873620479  

This is resolved by https://github.com/boostorg/config/pull/383, although if https://github.com/boostorg/config/pull/385 is merged it will be better to switch the check to use the new macro.
