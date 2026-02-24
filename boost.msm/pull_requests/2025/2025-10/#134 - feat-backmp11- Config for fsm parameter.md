# #134 feat/backmp11: Config for fsm parameter [Merged]

> Username: chandryan  
> Created at: 2025-10-13 21:35:36 UTC  
> Updated at: 2025-10-14 05:23:11 UTC  
> Merged at: 2025-10-14 05:23:08 UTC  
> Closed at: 2025-10-14 05:23:08 UTC  
> Url: https://github.com/boostorg/msm/pull/134  

Implemented a config `fsm_parameter` to decide whether the processing sm or the root sm gets passed to actions and guards.  
Applied further refactorings to the transition implementation to have such configs have an impact on less code locations.  
  
Also added a `is_state_active` API.

---
