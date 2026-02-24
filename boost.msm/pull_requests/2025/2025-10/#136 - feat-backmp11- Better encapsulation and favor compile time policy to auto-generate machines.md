# #136 feat/backmp11: Better encapsulation and favor compile time policy to auto-generate machines [Merged]

> Username: chandryan  
> Created at: 2025-10-18 19:02:35 UTC  
> Updated at: 2025-11-10 20:51:27 UTC  
> Merged at: 2025-10-18 22:47:55 UTC  
> Closed at: 2025-10-18 22:47:55 UTC  
> Url: https://github.com/boostorg/msm/pull/136  

Applied changes:  
  
- Moved as much of the state_machine non-public API to private visibility as possible (front-end API is still there but difficult to hide without breaking the metaprogramming)  
- Moved as many other functions as possible into detail namespace and/or sub-folder  
- Made favor_compile_time an optional include and changed it to automatically instantiate the SMs by default (can be adjusted with an opt-in preprocessor switch)  
- Removed the reset method again from the API for now, too many uncertainties how a proper reset looks like  
  
Almost no functional changes in this PR, unfortunately the diff still looks messy

---
