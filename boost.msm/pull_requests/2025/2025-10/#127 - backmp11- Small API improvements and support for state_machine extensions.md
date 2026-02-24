# #127 backmp11: Small API improvements and support for state_machine extensions [Merged]

> Username: chandryan  
> Created at: 2025-10-11 16:56:38 UTC  
> Updated at: 2025-11-10 20:52:32 UTC  
> Merged at: 2025-10-11 23:56:56 UTC  
> Closed at: 2025-10-11 23:56:56 UTC  
> Url: https://github.com/boostorg/msm/pull/127  

Applied changes:  
- Replaced `const int* current_state()`  with a `get_active_state_ids()` returning a std::array  
- Added an additional optional template parameter for supporting extensions of the state_machine class  
- Improved some other API points

---
