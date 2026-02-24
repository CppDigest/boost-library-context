# #65 Update work_sharing to never share a thread's main fiber. [Merged]

> Username: nat-goodspeed  
> Created at: 2015-09-07 18:52:59 UTC  
> Updated at: 2016-06-06 11:36:34 UTC  
> Merged at: 2015-09-07 18:55:26 UTC  
> Closed at: 2015-09-07 18:55:26 UTC  
> Url: https://github.com/boostorg/fiber/pull/65  

Instead, when the main fiber is passed to awakened(), stash it in a separate  
slot and make pick_next() return it only when the shared queue is empty.  
  
Update ready_fibers() to include a non-empty main_fiber slot.

---
