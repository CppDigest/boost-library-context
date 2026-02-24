# #196 Fix high contention on remote_ready_splk_  [Merged]

> Username: romange  
> Created at: 2019-03-10 09:34:29 UTC  
> Updated at: 2019-04-01 05:55:51 UTC  
> Merged at: 2019-04-01 05:55:51 UTC  
> Closed at: 2019-04-01 05:55:51 UTC  
> Url: https://github.com/boostorg/fiber/pull/196  

during the wake path caused by a local thread holding the lock for too long

---

## Comment 1

> Username: romange  
> Created_at: 2019-03-11 07:36:06 UTC  
> Updated_at: 2019-03-11 07:37:46 UTC  
> Url: https://github.com/boostorg/fiber/pull/196#issuecomment-471433408  

@olk  please take a look at this patch. This somewhat mitigates the performance bottleneck when few distinct threads synchronize via fiber synchronization primitives.

---

## Comment 2

> Username: olk  
> Created_at: 2019-04-01 05:55:46 UTC  
> Url: https://github.com/boostorg/fiber/pull/196#issuecomment-478445491  

ty

---
