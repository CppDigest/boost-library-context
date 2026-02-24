# #106 Less ambiguous definition for lock(...) functions [Merged]

> Username: apolukhin  
> Created at: 2016-12-04 00:02:14 UTC  
> Updated at: 2016-12-08 17:56:38 UTC  
> Merged at: 2016-12-08 17:56:38 UTC  
> Closed at: 2016-12-08 17:56:38 UTC  
> Url: https://github.com/boostorg/thread/pull/106  

Current definition may confuse library users, they may think that calling `lock(m1,m2)` and `lock(m1, m2,m3, m4)` may result in a deadlock (because they are calling `lock()` for different set of mutexes).

---

## Comment 1

> Username: viboes  
> Created_at: 2016-12-08 17:56:30 UTC  
> Url: https://github.com/boostorg/thread/pull/106#issuecomment-265807960  

Thanks

---
