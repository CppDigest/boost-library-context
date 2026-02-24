# #261 Fixes a memory corruption in logger and adds sanitizer builds to CI [Merged]

> Username: anarthal  
> Created at: 2025-05-30 10:15:03 UTC  
> Updated at: 2025-06-01 11:41:04 UTC  
> Merged at: 2025-06-01 11:40:38 UTC  
> Closed at: 2025-06-01 11:40:38 UTC  
> Url: https://github.com/boostorg/redis/pull/261  

logger now owns the prefix string  
This fixes a use-after-stack-return memory error in async_run  
Adds a clang-19 and a gcc-14 build to CI with the address and undefined behavior sanitizer enabled  
  
close #260

---

## Comment 1

> Username: anarthal  
> Created_at: 2025-05-30 10:16:14 UTC  
> Url: https://github.com/boostorg/redis/pull/261#issuecomment-2921917779  

I've chosen to just make the logger owning as you suggested because it's only used in long-running operations, so the overhead shouldn't be big.

---
