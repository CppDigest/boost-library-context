# #124 backmp11: Implement a state machine config with root sm support [Merged]

> Username: chandryan  
> Created at: 2025-10-09 20:57:55 UTC  
> Updated at: 2025-10-10 05:51:11 UTC  
> Merged at: 2025-10-10 05:51:08 UTC  
> Closed at: 2025-10-10 05:51:08 UTC  
> Url: https://github.com/boostorg/msm/pull/124  

Refactor the state machine signature to pass a single state machine config parameter struct, which bundles all previous parameters, instead of one parameter for each config setting.  
  
Additionally added support for setting a root sm in the state machine config. The root sm refers to the uppermost sm in a hierarchical state machine.

---
