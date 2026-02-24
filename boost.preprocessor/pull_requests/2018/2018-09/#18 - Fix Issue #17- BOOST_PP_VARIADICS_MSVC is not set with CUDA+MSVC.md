# #18 Fix Issue #17: BOOST_PP_VARIADICS_MSVC is not set [...] with CUDA+MSVC [Closed]

> Username: cedrou  
> Created at: 2018-09-06 16:35:08 UTC  
> Updated at: 2018-09-11 15:12:09 UTC  
> Closed at: 2018-09-11 15:12:09 UTC  
> Url: https://github.com/boostorg/preprocessor/pull/18  

This commit fixes the issue #17

---

## Comment 1

> Username: eldiener  
> Created_at: 2018-09-06 21:39:18 UTC  
> Url: https://github.com/boostorg/preprocessor/pull/18#issuecomment-419250078  

It does not seem to me that the __CUDACC__ definition should be used at all in config.h since the compilation of a .cu file is handed over to some other host compiler. So I do not understand why config.h should refer to that define in any situation.

---

## Comment 2

> Username: eldiener  
> Created_at: 2018-09-10 21:39:45 UTC  
> Url: https://github.com/boostorg/preprocessor/pull/18#issuecomment-420070424  

I have decide to just back out the change which was made to specify that CUDA files support variadic macros. It is possible that I should not turn off variadic macro support for CUDA files in config.h but rely on whatever other predefined compiler macros exist when compiling CUDA files, but until I can get definitive answers regarding what nvcc preprocessing entails on various platforms when compiling CUDA files, and what compiler macros are actually defined when this happens I think it is safer to just return to the previous situation. Thanks for everybody bringing up this issue, even if it is a real headache for myself as the current Boost PP maintainer.

---
