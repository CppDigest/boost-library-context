# #137 Fix some MSVC warnings [Merged]

> Username: Flamefire  
> Created at: 2022-11-18 13:34:16 UTC  
> Updated at: 2022-11-22 08:15:20 UTC  
> Merged at: 2022-11-22 08:15:17 UTC  
> Closed at: 2022-11-22 08:15:17 UTC  
> Url: https://github.com/boostorg/locale/pull/137  

Define `_SCL_SECURE_NO_WARNINGS` for building the library to avoid "Call to 'std::copy' with parameters that may be unsafe" in `src\boost\locale\std\converter.cpp`  
This also allows to remove the multiple suppressions of C4996  
  
Introduce wrappers with suppressed warning in test code so that the public headers will still throw the warning if there is any (which must not be the case as headers are supposed to be warning-free)  
  
Closes #136

---
