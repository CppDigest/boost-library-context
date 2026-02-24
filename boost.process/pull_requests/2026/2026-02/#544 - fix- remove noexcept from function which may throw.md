# #544 fix: remove noexcept from function which may throw [Open]

> Username: phorv  
> Created at: 2026-02-16 09:21:47 UTC  
> Updated at: 2026-02-16 09:21:47 UTC  
> Url: https://github.com/boostorg/process/pull/544  

wait(const group_handle &p) is the throwing version, should not be marked noexcept.

---
