# #55 CMake: Fix required C++ standard [Merged]

> Username: Flamefire  
> Created at: 2025-09-06 10:17:58 UTC  
> Updated at: 2025-09-07 09:12:30 UTC  
> Merged at: 2025-09-06 14:09:41 UTC  
> Closed at: 2025-09-06 14:09:41 UTC  
> Url: https://github.com/boostorg/heap/pull/55  

`cxx_std_14` is only available since CMake 3.8  
The `CXX_STANDARD_REQUIRED` property is a boolean, i.e. ON or OFF not a numeric value. The feature is enough to require the standard already

---

## Comment 1

> Username: nigels-com  
> Created_at: 2025-09-06 13:38:10 UTC  
> Url: https://github.com/boostorg/heap/pull/55#issuecomment-3262188258  

Ah, nice.  Thanks.

---
