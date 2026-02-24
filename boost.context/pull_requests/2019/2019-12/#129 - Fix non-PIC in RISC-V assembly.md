# #129 Fix non-PIC in RISC-V assembly [Merged]

> Username: andreas-schwab  
> Created at: 2019-12-06 21:51:51 UTC  
> Updated at: 2019-12-08 15:54:44 UTC  
> Merged at: 2019-12-08 15:54:37 UTC  
> Closed at: 2019-12-08 15:54:37 UTC  
> Url: https://github.com/boostorg/context/pull/129  

Since this ends up in a shared library we should use a PLT call.  Also,  
the finish label is local, so we can use lla instead of la.

---

## Comment 1

> Username: olk  
> Created_at: 2019-12-08 15:54:44 UTC  
> Url: https://github.com/boostorg/context/pull/129#issuecomment-562962717  

ty

---
