# #320 Renamed forced_unwind struct to fiber_forced_unwind in fiber_ucontext [Merged]

> Username: mrmitzh  
> Created at: 2025-11-20 02:37:20 UTC  
> Updated at: 2025-12-27 15:26:19 UTC  
> Merged at: 2025-12-27 15:26:15 UTC  
> Closed at: 2025-12-27 15:26:15 UTC  
> Url: https://github.com/boostorg/context/pull/320  

Renamed forced_unwind struct to fiber_forced_unwind in fiber_context.hpp to resolve symbol collision issue when using ucontext in fiber and continuation (same struct is defined in continuation_ucontext.hpp:L219).

---

## Comment 1

> Username: olk  
> Created_at: 2025-12-27 15:26:19 UTC  
> Url: https://github.com/boostorg/context/pull/320#issuecomment-3694042620  

ty

---
