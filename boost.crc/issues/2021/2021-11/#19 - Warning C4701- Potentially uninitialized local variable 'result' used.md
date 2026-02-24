# #19 - Warning C4701: Potentially uninitialized local variable 'result' used [Closed]

> Username: Morph1984  
> Created at: 2021-11-17 21:35:51 UTC  
> Updated at: 2024-04-13 17:42:08 UTC  
> Closed at: 2024-04-13 17:42:08 UTC  
> Url: https://github.com/boostorg/crc/issues/19  

The following error is encountered here when compiling in MSVC  
https://github.com/boostorg/crc/blob/c80e31f78a248e33480ced76c4b03a0b71a5c4db/include/boost/crc.hpp#L971

---

## Comment 1

> Username: pdimov  
> Created at: 2024-04-13 17:42:08 UTC  
> Url: https://github.com/boostorg/crc/issues/19#issuecomment-2053712629  

Should have been fixed by https://github.com/boostorg/crc/pull/6.
