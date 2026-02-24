# #198 :bug: Fix bit_ceil() to return 1 for input 0 as per specification [Closed]

> Username: Pega5us  
> Created at: 2025-06-06 09:11:07 UTC  
> Updated at: 2025-06-06 09:19:02 UTC  
> Closed at: 2025-06-06 09:18:50 UTC  
> Url: https://github.com/boostorg/core/pull/198  

## :bug: #197  Fix `bit_ceil()` to return 1 for input 0 as per specification  
  
This PR fixes a bug in `boost::core::bit_ceil()` where the function returned `0` for an input of `0`. According to the specification, it should return `1`.  
  
### 🔧 Changes  
- Corrected the implementation of `bit_ceil()` to return `1` when called with `0`.  
- Added test coverage to validate the corrected behavior.  
  
### ✅ Impact  
Aligns the function with the expected and documented behavior. This fix prevents potential logical errors in code relying on `bit_ceil(0)` returning `1`.

---
