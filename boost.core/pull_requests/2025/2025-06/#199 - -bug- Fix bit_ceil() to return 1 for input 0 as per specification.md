# #199 :bug: Fix bit_ceil() to return 1 for input 0 as per specification [Merged]

> Username: Pega5us  
> Created at: 2025-06-06 09:21:47 UTC  
> Updated at: 2025-06-06 15:17:15 UTC  
> Merged at: 2025-06-06 15:17:15 UTC  
> Closed at: 2025-06-06 15:17:15 UTC  
> Url: https://github.com/boostorg/core/pull/199  

## :bug: #197  Fix `bit_ceil()` to return 1 for input 0 as per specification  
  
This PR fixes a bug in `boost::core::bit_ceil()` where the function returned `0` for an input of `0`. According to the specification, it should return `1`.  
  
### 🔧 Changes  
- Corrected the implementation of `bit_ceil()` to return `1` when called with `0`.  
- Added test coverage to validate the corrected behaviour.  
  
### ✅ Impact  
Aligns the function with the expected and documented behaviour. This fix prevents potential logical errors in code relying on `bit_ceil(0)` returning `1`.

---
