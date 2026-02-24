# #216 - Enforce minimum version of MSVC compiler [Closed]

> Username: PROGrand  
> Created at: 2016-12-30 10:11:04 UTC  
> Updated at: 2017-04-19 15:43:43 UTC  
> Closed at: 2017-04-19 15:43:43 UTC  
> Url: https://github.com/boostorg/beast/issues/216  

Usage beast::detail::void_t template for type safety breaks compilation.   
Constructions like  
```  
template<class T, class M, class = beast::detail::void_t<>>  
struct is_Reader : std::false_type {};  
  
template<class T, class M>  
struct is_Reader<T, M, beast::detail::void_t<decltype( ... some ... )>>  
```  
are always interpreted as first definition (false_type).  
  
Removing beast::detail::void_t allows to compile and successive run.  
```  
template<class T, class M, class = beast::detail::void_t<>>  
struct is_Reader : std::false_type {};  
  
template<class T, class M>  
struct is_Reader<T, M, decltype( ... some ... )>  
```

---

## Comment 1

> Username: vinniefalco  
> Created at: 2016-12-30 13:05:07 UTC  
> Url: https://github.com/boostorg/beast/issues/216#issuecomment-269769516  

Isn't the latest version is Visual Studio 2015 Update 3? Do you get the same error with the more recent version?

---

## Comment 2

> Username: PROGrand  
> Created at: 2017-01-08 10:14:54 UTC  
> Url: https://github.com/boostorg/beast/issues/216#issuecomment-271141938  

2015 Update 1. Important tool in my development environment (nvcc from Nvidia CUDA Toolkit actually) does not support updates newer than 1.

---

## Comment 3

> Username: vinniefalco  
> Created at: 2017-01-08 11:58:59 UTC  
> Url: https://github.com/boostorg/beast/issues/216#issuecomment-271146542  

Visual Studio 2015 Update 1 has significant defects in its support for C++11, such as the bug with SFINAE that you uncovered. I don't have the resources to support those older versions.

---

## Comment 4

> Username: vinniefalco  
> Created at: 2017-01-10 23:49:06 UTC  
> Url: https://github.com/boostorg/beast/issues/216#issuecomment-271735129  

I'll close this, since its not a sustainable solution.

---

## Comment 5

> Username: vinniefalco  
> Created at: 2017-04-10 16:16:59 UTC  
> Updated at: 2017-04-10 16:17:31 UTC  
> Url: https://github.com/boostorg/beast/issues/216#issuecomment-293000010  

At the least, Beast should contain header mechanics to detect the version of Visual Studio:  
  
`_MSC_VER` and `_MSC_FULL_VER` by version:  
```  
14.0 (2015)	        1900	190023026  
14.0 (2015 Update 1)	1900	190023506  
14.0 (2015 Update 2)	1900	190023918  
14.0 (2015 Update 3)	1900	190024210  
```  
  
Source:  
https://sourceforge.net/p/predef/wiki/Compilers/
