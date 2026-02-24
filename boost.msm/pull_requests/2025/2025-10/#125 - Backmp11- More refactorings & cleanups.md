# #125 Backmp11: More refactorings & cleanups [Merged]

> Username: chandryan  
> Created at: 2025-10-11 09:19:09 UTC  
> Updated at: 2025-11-10 20:52:34 UTC  
> Merged at: 2025-10-11 12:03:26 UTC  
> Closed at: 2025-10-11 12:03:26 UTC  
> Url: https://github.com/boostorg/msm/pull/125  

Applied changes in backmp11:  
- Deprecated boost::any in favor of std::any  
- Removed support of the fsm check policy  
- Refactored the transition implementations with `if constexpr` for more code re-usage

---
