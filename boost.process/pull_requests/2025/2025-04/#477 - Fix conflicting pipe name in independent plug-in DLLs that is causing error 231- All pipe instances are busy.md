# #477 Fix conflicting pipe name in independent plug-in DLLs that is causing error 231: All pipe instances are busy  [Merged]

> Username: RK-BFX  
> Created at: 2025-04-10 18:23:55 UTC  
> Updated at: 2025-04-14 15:48:00 UTC  
> Merged at: 2025-04-14 15:48:00 UTC  
> Closed at: 2025-04-14 15:48:00 UTC  
> Url: https://github.com/boostorg/process/pull/477  

Include numerical representation of local static variable's address into the pipe name to discriminate Boost.Process instances in independent DLLs.  
  
Fixes #476

---
