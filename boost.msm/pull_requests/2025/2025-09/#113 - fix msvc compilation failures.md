# #113 fix msvc compilation failures [Merged]

> Username: chandryan  
> Created at: 2025-09-27 10:31:07 UTC  
> Updated at: 2025-09-28 04:02:40 UTC  
> Merged at: 2025-09-27 12:48:47 UTC  
> Closed at: 2025-09-27 12:48:47 UTC  
> Url: https://github.com/boostorg/msm/pull/113  

Fix compilation failures when compiling with MSVC:  
- Some tests ran out of heap on 32-bit, changed the test Jamfile to only execute them on 64-bit  
- MSVC has issues working with init cell constants in backmp11, implemented a workaround  
  
Related to https://github.com/boostorg/msm/issues/105

---
