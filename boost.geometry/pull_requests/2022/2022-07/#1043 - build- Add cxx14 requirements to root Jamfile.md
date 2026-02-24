# #1043 build: Add cxx14 requirements to root Jamfile [Merged]

> Username: mloskot  
> Created at: 2022-07-21 22:00:24 UTC  
> Updated at: 2022-08-03 08:18:21 UTC  
> Merged at: 2022-07-22 15:15:04 UTC  
> Closed at: 2022-07-22 15:15:04 UTC  
> Url: https://github.com/boostorg/geometry/pull/1043  

The library requires C++14 so it should not even be tried to build with any older `cxxstd`  
  
IOW, `b2 cxxstd=11 libs/geometry/test` will build nothing.  
  
Related to discussion in #1012

---

## Review 1 [Approved]

> Username: vissarion  
> Created_at: 2022-07-22 12:40:09 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/geometry/pull/1043#pullrequestreview-1047846198  

Thanks!

---
