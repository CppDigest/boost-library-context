# #74 stack, spsc_queue: don't create temporaries when deleting elements [Merged]

> Username: msuvajac  
> Created at: 2021-09-17 08:15:47 UTC  
> Updated at: 2021-09-17 08:23:40 UTC  
> Merged at: 2021-09-17 08:23:35 UTC  
> Closed at: 2021-09-17 08:23:35 UTC  
> Url: https://github.com/boostorg/lockfree/pull/74  

When destructors/reset() are called temporary object was being created.  
That creates issues when element doesn't have default initialization  
(e.g. Boost.Interprocess containers).

---

## Comment 1

> Username: timblechmann  
> Created_at: 2021-09-17 08:23:40 UTC  
> Url: https://github.com/boostorg/lockfree/pull/74#issuecomment-921606767  

thanks!

---
