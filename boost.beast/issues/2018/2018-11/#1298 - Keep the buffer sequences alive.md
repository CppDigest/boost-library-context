# #1298 - Keep the buffer sequences alive [Closed]

> Username: vinniefalco  
> Created at: 2018-11-12 21:22:18 UTC  
> Updated at: 2018-12-13 00:13:03 UTC  
> Closed at: 2018-12-13 00:13:03 UTC  
> Url: https://github.com/boostorg/beast/issues/1298  

Asynchronous operations must maintain at least one copy of any used buffer sequence for the lifetime of the operation. I might have been over-zealous in "optimizing" one or more asynchronous operations to do work in the constructor, and not maintain a copy of the buffer (to save space).

---

## Comment 1

> Username: stale[bot]  
> Created at: 2018-12-12 21:49:01 UTC  
> Url: https://github.com/boostorg/beast/issues/1298#issuecomment-446756882  

This issue has been open for a while with no activity, has it been resolved?
