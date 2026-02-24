# #21 Support MSVC-7.1/ intrin.h available >= MSVC-8.0 [Merged]

> Username: igaztanaga  
> Created at: 2015-10-18 10:51:23 UTC  
> Updated at: 2015-10-21 22:38:22 UTC  
> Merged at: 2015-10-21 22:38:22 UTC  
> Closed at: 2015-10-21 22:38:22 UTC  
> Url: https://github.com/boostorg/smart_ptr/pull/21  

-> MSVC 7.1 has no intrin.h and needs to use "#pragma intrinsic".  
-> intrin.h available since Visual 2005

---

## Comment 1

> Username: pdimov  
> Created_at: 2015-10-18 13:21:04 UTC  
> Updated_at: 2015-10-18 17:44:17 UTC  
> Url: https://github.com/boostorg/smart_ptr/pull/21#discussion_r42320877  

This change is wrong.

---
