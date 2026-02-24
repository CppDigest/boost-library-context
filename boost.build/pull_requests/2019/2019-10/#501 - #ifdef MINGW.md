# #501 #ifdef MINGW ... [Closed]

> Username: ghost  
> Created at: 2019-10-31 13:02:00 UTC  
> Updated at: 2021-10-02 21:13:44 UTC  
> Closed at: 2019-10-31 14:28:28 UTC  
> Url: https://github.com/boostorg/build/pull/501  

Replaced with "#if defined(__MINGW64__) || defined(__MINGW32__)" to enable both MinGW and MinGW-w64 compilers.

---
