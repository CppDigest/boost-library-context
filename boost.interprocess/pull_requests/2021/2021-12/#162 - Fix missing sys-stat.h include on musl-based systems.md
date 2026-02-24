# #162 Fix missing sys/stat.h include on musl-based systems [Merged]

> Username: leonardohn  
> Created at: 2021-12-13 04:13:06 UTC  
> Updated at: 2021-12-26 21:53:28 UTC  
> Merged at: 2021-12-26 21:53:19 UTC  
> Closed at: 2021-12-26 21:53:19 UTC  
> Url: https://github.com/boostorg/interprocess/pull/162  

Boost 1.78.0 fails to build on musl-based systems because musl does  
not include sys/stat.h by default.  
  
Fixes #161 ("Boost compiler error")

---

## Comment 1

> Username: igaztanaga  
> Created_at: 2021-12-26 21:53:28 UTC  
> Url: https://github.com/boostorg/interprocess/pull/162#issuecomment-1001246198  

Many thanks for the patch!

---
