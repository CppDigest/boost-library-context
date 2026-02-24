# #1076 - Composed operations need executor_work_guard [Closed]

> Username: vinniefalco  
> Created at: 2018-03-23 16:29:40 UTC  
> Updated at: 2018-04-24 01:19:57 UTC  
> Closed at: 2018-04-24 01:19:57 UTC  
> Url: https://github.com/boostorg/beast/issues/1076  

All composed operations, including in the examples, tutorials, and test code (e.g. `test::stream`) need to make sure an `executor_work_guard` constructed on the I/O context's executor remains in existence until before the upcall. The work object can be cleared using `executor_work_guard::reset()`.  
  
This needs to be documented in the tutorial for composed operations as it is required for Net.TS.  
  
We should also consider how Beast might provide utility classes and functions to make this other parts of writing composed operations easier.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2018-03-23 19:28:36 UTC  
> Url: https://github.com/boostorg/beast/issues/1076#issuecomment-375774569  

Not to be confused with the `executor_work_guard` also constructed for the handler's associated executor.
