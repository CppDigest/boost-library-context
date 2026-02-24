# #56 - CMake errors on master [Closed]

> Username: pdimov  
> Created at: 2025-10-17 15:17:24 UTC  
> Updated at: 2025-10-18 00:48:55 UTC  
> Closed at: 2025-10-18 00:48:55 UTC  
> Url: https://github.com/boostorg/heap/issues/56  

```  
CMake Error at libs/heap/CMakeLists.txt:22 (set_target_properties):  
  INTERFACE_LIBRARY targets may only have whitelisted properties.  The  
  property "CMAKE_CXX_STANDARD_REQUIRED" is not allowed.  
```  
Happens on CMake up to and including 3.18. 3.16 is default on Ubuntu 20.04 LTS.  
  
https://github.com/boostorg/cmake/actions/runs/18593803749/job/53014800525  
https://github.com/boostorg/cmake/actions/runs/18593803742/job/53014800971

---

## Comment 1

> Username: timblechmann  
> Created at: 2025-10-18 00:48:55 UTC  
> Url: https://github.com/boostorg/heap/issues/56#issuecomment-3417642474  

by bad, the fix was only on `develop`. merged into `master` now
