# #123 - CMake errors on master [Closed]

> Username: pdimov  
> Created at: 2025-10-17 15:20:02 UTC  
> Updated at: 2025-10-18 00:49:10 UTC  
> Closed at: 2025-10-18 00:49:10 UTC  
> Url: https://github.com/boostorg/lockfree/issues/123  

```  
CMake Error at libs/lockfree/CMakeLists.txt:45 (set_target_properties):  
  INTERFACE_LIBRARY targets may only have whitelisted properties.  The  
  property "CXX_STANDARD_REQUIRED" is not allowed.  
```  
Happens on CMake up to and including 3.18. 3.16 is default on Ubuntu 20.04 LTS.  
  
https://github.com/boostorg/cmake/actions/runs/18593803749/job/53014800525  
https://github.com/boostorg/cmake/actions/runs/18593803742/job/53014800971

---

## Comment 1

> Username: timblechmann  
> Created at: 2025-10-18 00:49:10 UTC  
> Url: https://github.com/boostorg/lockfree/issues/123#issuecomment-3417642634  

by bad, the fix was only on `develop`. merged into `master` now
