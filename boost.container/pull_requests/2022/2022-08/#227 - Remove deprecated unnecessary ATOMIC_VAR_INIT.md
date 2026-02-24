# #227 Remove deprecated unnecessary ATOMIC_VAR_INIT [Closed]

> Username: ecatmur  
> Created at: 2022-08-12 12:23:56 UTC  
> Updated at: 2022-12-11 19:40:43 UTC  
> Closed at: 2022-12-11 19:40:42 UTC  
> Url: https://github.com/boostorg/container/pull/227  

`ATOMIC_VAR_INIT` has always been unnecessary (it does the same as the converting ctor of `std::atomic`) and is deprecated in C++20.  
  
This causes build failure for clang/libc++/Wall.  
  
Use direct-initialization syntax so that it works in pre-C++17 (?) mode  
  
ref. #211

---

## Comment 1

> Username: ecatmur  
> Created_at: 2022-08-13 21:04:50 UTC  
> Updated_at: 2022-08-13 21:18:21 UTC  
> Url: https://github.com/boostorg/container/pull/227#issuecomment-1214222404  

I can't see why the Windows 2022 gcc-11 CI build is failing, the logs don't appear to show anything useful.  
  
edit: it has been pointed out to me that the -1073741511 appearing in the logs is 0xC0000139 which indicates a problem loading DLLs, so probably not caused by this change.

---

## Comment 2

> Username: igaztanaga  
> Created_at: 2022-12-11 19:40:42 UTC  
> Url: https://github.com/boostorg/container/pull/227#issuecomment-1345638960  

Thanks for the report! The fix for #232 partially solved the issue plus I added direct initialization as you suggested in commit https://github.com/boostorg/container/commit/e9b09a5ee7211091d8e7398471eb6265fc452e1e.

---
