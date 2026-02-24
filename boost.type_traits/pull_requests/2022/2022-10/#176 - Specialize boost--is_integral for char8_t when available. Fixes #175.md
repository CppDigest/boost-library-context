# #176 Specialize boost::is_integral for char8_t when available. Fixes #175. [Merged]

> Username: pdimov  
> Created at: 2022-10-30 00:46:49 UTC  
> Updated at: 2022-11-12 12:34:44 UTC  
> Merged at: 2022-11-06 18:37:51 UTC  
> Closed at: 2022-11-06 18:37:51 UTC  
> Url: https://github.com/boostorg/type_traits/pull/176  



---

## Comment 1

> Username: jzmaddock  
> Created_at: 2022-11-06 18:37:44 UTC  
> Url: https://github.com/boostorg/type_traits/pull/176#issuecomment-1304865691  

Failing tests are unrelated and inscrutable so merging.

---

## Comment 2

> Username: pdimov  
> Created_at: 2022-11-06 18:40:55 UTC  
> Url: https://github.com/boostorg/type_traits/pull/176#issuecomment-1304866295  

The XCode failures seem to mostly be caused by a missing but used `std::declval`, although there might be something else as well.  
  
GCC 4.4 -std=c++0x fails some "tricky" tests because of who knows why.

---

## Comment 3

> Username: jzmaddock  
> Created_at: 2022-11-06 18:42:14 UTC  
> Url: https://github.com/boostorg/type_traits/pull/176#issuecomment-1304866576  

I know, but nothing has changed around here, so I'm perplexed.

---

## Comment 4

> Username: pdimov  
> Created_at: 2022-11-06 18:57:31 UTC  
> Url: https://github.com/boostorg/type_traits/pull/176#issuecomment-1304869138  

The new run has these  
```  
clang: warning: libstdc++ is deprecated; move to libc++ with a minimum deployment target of OS X 10.9 [-Wdeprecated]  
```  
which is odd. And sure enough, the old runs that succeed such as [this one](https://drone.cpp.al/boostorg/type_traits/44/30/2) use libc++  
```  
libc++ version 5000  
    _LIBCPP_VERSION                         =5000  
```  
but the [failing run](https://drone.cpp.al/boostorg/type_traits/55/30/2) uses libstdc++:  
```  
GNU libstdc++ version 20070719  
    __GLIBCXX__                             =20070719  
    _GLIBCXX_USE_WCHAR_T                    =1  
    _GLIBCXX_USE_LONG_LONG                  =1  
    _GLIBCXX_USE_C99_MATH                   =1  
    _GLIBCXX_USE_C99                        =1  
    _GLIBCXX_SYMVER                         =1  
    _GLIBCXX_HOSTED                         =1  
```  
This is either caused by a change in the images or by a change in Boost.CI - depending on which of these installs GCC.  
  
@sdarwin @Flamefire

---

## Comment 5

> Username: pdimov  
> Created_at: 2022-11-06 19:07:19 UTC  
> Url: https://github.com/boostorg/type_traits/pull/176#issuecomment-1304871472  

The GCC 4.4 failures  
```  
./boost/config/stdlib/libstdcpp3.hpp:262: error: "_GLIBCXX_USE_DEPRECATED" is not defined  
```  
are probably caused by https://github.com/boostorg/type_traits/commit/d71524a79958942c3a9cbd93099b5180c490a68a which adds  
```  
   <toolset>gcc:<cxxflags>-Wundef  
```  
to test/Jamfile.v2.

---

## Comment 6

> Username: pdimov  
> Created_at: 2022-11-06 19:09:58 UTC  
> Url: https://github.com/boostorg/type_traits/pull/176#issuecomment-1304871981  

No idea whether the use of `_GLIBCXX_USE_DEPRECATED` at https://github.com/boostorg/config/blob/2543ff139707a32cc421b62c9d65bb4d9c965b3e/include/boost/config/stdlib/libstdcpp3.hpp#L262 is correct or not for GCC 4.4.

---

## Comment 7

> Username: Flamefire  
> Created_at: 2022-11-12 12:34:43 UTC  
> Url: https://github.com/boostorg/type_traits/pull/176#issuecomment-1312469461  

> The GCC 4.4 failures  
>   
> ```  
> ./boost/config/stdlib/libstdcpp3.hpp:262: error: "_GLIBCXX_USE_DEPRECATED" is not defined  
> ```  
>   
> are probably caused by [d71524a](https://github.com/boostorg/type_traits/commit/d71524a79958942c3a9cbd93099b5180c490a68a) which adds  
  
  
Correct although confusing: There is a check `if BOOST_LIBSTDCXX_VERSION < 40600` before so the define shouldn't be checked. Anyway opened a PR to fix that by checking if it is define first.

---
