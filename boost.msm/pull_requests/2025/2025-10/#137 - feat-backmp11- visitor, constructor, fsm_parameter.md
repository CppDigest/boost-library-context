# #137 feat/backmp11: visitor, constructor, fsm_parameter [Merged]

> Username: chandryan  
> Created at: 2025-10-19 12:43:10 UTC  
> Updated at: 2025-11-10 20:51:28 UTC  
> Merged at: 2025-10-19 21:46:35 UTC  
> Closed at: 2025-10-19 21:46:35 UTC  
> Url: https://github.com/boostorg/msm/pull/137  

Applied changes:  
- Improved and extended the visitor API to be easier to use. It now supports 4 different combinations which states to visit and uses an enum for selection instead of tag dispatch  
- Improved the constructor to resolve ambiguities when Derived is not set. Also some fixes and lots of compile time assertions for wrongly instantiated state machines  
- Corrected the name default fsm parameter; used transition owner for now, because it's not the processing sm

---
