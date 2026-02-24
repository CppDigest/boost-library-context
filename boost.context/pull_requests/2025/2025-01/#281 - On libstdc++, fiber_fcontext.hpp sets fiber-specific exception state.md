# #281 On libstdc++, fiber_fcontext.hpp sets fiber-specific exception state. [Merged]

> Username: nat-goodspeed  
> Created at: 2025-01-13 16:38:26 UTC  
> Updated at: 2025-01-14 11:52:38 UTC  
> Merged at: 2025-01-14 11:52:32 UTC  
> Closed at: 2025-01-14 11:52:32 UTC  
> Url: https://github.com/boostorg/context/pull/281  

This makes use of the libstdc++ published entry point `__cxa_get_globals()`, which retrieves a pointer to the `__cxa_eh_globals` struct. Saving and restoring that struct on every context switch ensures fiber-specific exception behavior.

---

## Comment 1

> Username: olk  
> Created_at: 2025-01-14 11:52:36 UTC  
> Url: https://github.com/boostorg/context/pull/281#issuecomment-2589716169  

ty

---
