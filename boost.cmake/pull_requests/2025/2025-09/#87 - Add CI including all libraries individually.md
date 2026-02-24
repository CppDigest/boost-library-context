# #87 Add CI including all libraries individually [Merged]

> Username: Flamefire  
> Created at: 2025-09-11 10:42:00 UTC  
> Updated at: 2025-09-11 13:37:33 UTC  
> Merged at: 2025-09-11 13:08:35 UTC  
> Closed at: 2025-09-11 13:08:35 UTC  
> Url: https://github.com/boostorg/cmake/pull/87  

Split out from #86   
  
Add a CI job that loops over all Boost libraries and configures Boost with only that library in `BOOST_INCLUDE_LIBRARY` to verify `BoostRoot.cmake` is handling that correctly.

---

## Comment 1

> Username: Flamefire  
> Created_at: 2025-09-11 10:57:33 UTC  
> Url: https://github.com/boostorg/cmake/pull/87#issuecomment-3279930271  

- Log: Failure needs special case for ASIO  
- Process failure needs handling of one-line Boost library dependencies.  
  
Fixed both in #86

---
