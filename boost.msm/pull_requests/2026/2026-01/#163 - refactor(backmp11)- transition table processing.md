# #163 refactor(backmp11): transition table processing [Merged]

> Username: chandryan  
> Created at: 2026-01-23 01:01:37 UTC  
> Updated at: 2026-01-23 14:45:53 UTC  
> Merged at: 2026-01-23 14:45:44 UTC  
> Closed at: 2026-01-23 14:45:44 UTC  
> Url: https://github.com/boostorg/msm/pull/163  

Applied changes:  
- refactored the transition table processing  
  - moved all functions related to transition table processing to transition_table.hpp  
  - clearer separation between transition tables and internal transition tables for processing and dispatch table creation  
- optimized the transition table processing  
  - transition tables are not expanded recursively anymore  
  - recursion is now only used to evaluate if forward transitions are needed, and a single forward transition is added if evaluated as needed  
- all dispatch tables now get initialized with init cell constants instead of `mp11::mp_for_each` to reduce the amount of generated functions  
- rows in internal transition tables of state machines now also support event hierarchies and Kleene events

---
