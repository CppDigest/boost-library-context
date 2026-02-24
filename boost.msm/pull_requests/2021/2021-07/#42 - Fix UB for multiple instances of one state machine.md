# #42 Fix UB for multiple instances of one state machine [Merged]

> Username: joergczeranski  
> Created at: 2021-07-20 14:32:58 UTC  
> Updated at: 2023-08-29 23:15:27 UTC  
> Merged at: 2023-08-29 23:15:27 UTC  
> Closed at: 2023-08-29 23:15:27 UTC  
> Url: https://github.com/boostorg/msm/pull/42  

Don't re-initialize flags_entries every time get_entries_for_flag is  
called, only once per function template instance.  
This fixes a write-write race condition (undefined behavior) when  
multiple threads/strands instantiate the same state machine.

---
