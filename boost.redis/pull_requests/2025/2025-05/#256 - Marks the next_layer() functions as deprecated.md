# #256 Marks the next_layer() functions as deprecated [Merged]

> Username: anarthal  
> Created at: 2025-05-21 11:05:11 UTC  
> Updated at: 2025-05-25 09:05:10 UTC  
> Merged at: 2025-05-25 09:05:07 UTC  
> Closed at: 2025-05-25 09:05:07 UTC  
> Url: https://github.com/boostorg/redis/pull/256  



---

## Comment 1

> Username: anarthal  
> Created_at: 2025-05-21 11:06:43 UTC  
> Updated_at: 2025-05-21 11:06:56 UTC  
> Url: https://github.com/boostorg/redis/pull/256#issuecomment-2897541255  

This prepares the library to implement #246 (ETA Boost 1.90).  
Note that `BOOST_DEPRECATED` is used instead of `[[deprecated(M)]]` because it supports being disabled by defining `BOOST_ALLOW_DEPRECATED`.

---
