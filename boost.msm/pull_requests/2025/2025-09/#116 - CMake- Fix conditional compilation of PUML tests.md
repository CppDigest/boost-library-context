# #116 CMake: Fix conditional compilation of PUML tests [Merged]

> Username: Flamefire  
> Created at: 2025-09-28 11:26:09 UTC  
> Updated at: 2025-09-28 12:38:47 UTC  
> Merged at: 2025-09-28 12:17:19 UTC  
> Closed at: 2025-09-28 12:17:19 UTC  
> Url: https://github.com/boostorg/msm/pull/116  

When `CMAKE_CXX_STANDARD` is not set the LESS condition is not satisfied and the puml tests are still added even if the compiler doesn't support it or it isn't enabled.  
  
Check for it being set first.  
Otherwise add those tests as a separate target if the compiler does support C++20.

---
