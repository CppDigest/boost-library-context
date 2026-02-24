# #24 Change some doc references from 'algorithm' to 'sched_algorithm'. [Closed]

> Username: nat-goodspeed  
> Created at: 2014-09-22 14:07:09 UTC  
> Updated at: 2016-06-06 11:36:33 UTC  
> Closed at: 2014-09-22 17:12:37 UTC  
> Url: https://github.com/boostorg/fiber/pull/24  

Add some explanatory material in scheduling.qbk about coding your own  
scheduler and setting it with set_scheduling_algorithm(). Document  
sched_algorithm interface class.  
  
Fix the example in condition_variables.qbk to explicitly unlock 'lk' before  
calling process_data().

---

## Comment 1

> Username: olk  
> Created_at: 2014-09-22 17:12:35 UTC  
> Url: https://github.com/boostorg/fiber/pull/24#issuecomment-56406285  

merged into branches coro and ctx

---
