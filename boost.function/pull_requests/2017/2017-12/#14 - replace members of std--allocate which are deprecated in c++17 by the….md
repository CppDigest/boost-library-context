# #14 replace members of std::allocate which are deprecated in c++17 by the… [Merged]

> Username: DanielaE  
> Created at: 2017-12-16 19:05:51 UTC  
> Updated at: 2017-12-19 16:04:57 UTC  
> Merged at: 2017-12-18 22:35:14 UTC  
> Closed at: 2017-12-18 22:35:14 UTC  
> Url: https://github.com/boostorg/function/pull/14  

…irs cousins from std::allocator_traits.

---

## Comment 1

> Username: DanielaE  
> Created_at: 2017-12-16 19:23:34 UTC  
> Url: https://github.com/boostorg/function/pull/14#issuecomment-352205383  

It looks like Travis isn't configured correctly for Linux clang.

---

## Comment 2

> Username: eldiener  
> Created_at: 2017-12-16 19:49:25 UTC  
> Url: https://github.com/boostorg/function/pull/14#issuecomment-352206812  

We do not want to be checking for MSVC in particular. Instead what we want is to be using the config macro BOOST_NO_CXX11_ALLOCATOR to determine what the code should be. So please remove all the:  
  
#if !((__cplusplus > 201700) || (defined(_MSVC_LANG) && (_MSVC_LANG > 201700)))  
  
and substitute in its place:  
  
#if defined(BOOST_NO_CXX11_ALLOCATOR)  
  
then test.

---

## Comment 3

> Username: pdimov  
> Created_at: 2017-12-16 23:42:15 UTC  
> Url: https://github.com/boostorg/function/pull/14#issuecomment-352219944  

I updated `.travis.yml`. g++ 7 in C++17 mode fails one test though, due to a compiler bug: https://gcc.gnu.org/bugzilla/show_bug.cgi?id=81311. I reported that 5 months ago but nothing seems to have happened on that front, so perhaps we need to `#ifdef` that out for g++ 7 in C++17 mode?

---

## Comment 4

> Username: pdimov  
> Created_at: 2017-12-17 00:19:29 UTC  
> Url: https://github.com/boostorg/function/pull/14#issuecomment-352221610  

Yes, we do need to `#ifdef` it out, and we did, in https://github.com/boostorg/function/commit/eba48932f0904e49c773cf2b8981c4df26100636. :-)

---

## Comment 5

> Username: DanielaE  
> Created_at: 2017-12-17 06:21:04 UTC  
> Url: https://github.com/boostorg/function/pull/14#issuecomment-352235040  

@eldiener   
> and substitute in its place:  
> #if defined(BOOST_NO_CXX11_ALLOCATOR)  
  
Great - done. That was my preferred solution in the first place. But then I chose to follow Boost.Config's precedent on this particular topic ...

---

## Comment 6

> Username: eldiener  
> Created_at: 2017-12-18 22:35:39 UTC  
> Url: https://github.com/boostorg/function/pull/14#issuecomment-352580077  

Thanks for implementing this change.

---
