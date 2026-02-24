# #135 backmp11: refactorings, optimizations, reset method [Merged]

> Username: chandryan  
> Created at: 2025-10-17 23:04:52 UTC  
> Updated at: 2025-11-10 20:52:09 UTC  
> Merged at: 2025-10-18 06:27:50 UTC  
> Closed at: 2025-10-18 06:27:50 UTC  
> Url: https://github.com/boostorg/msm/pull/135  

Applied changes in backmp11:  
  
- Removed leftovers from eUML support  
- Removed most of the MPL occurrences  
- Removed most of the helper structs  
- Removed unused metafunctions  
- Optimized O(N) dispatches to O(1) in entries and exits  
- Aligned internal API names  
- Implemented a reset method

---
