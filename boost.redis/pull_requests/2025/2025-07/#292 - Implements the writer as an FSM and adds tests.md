# #292 Implements the writer as an FSM and adds tests [Closed]

> Username: anarthal  
> Created at: 2025-07-27 20:56:02 UTC  
> Updated at: 2025-10-22 10:02:03 UTC  
> Closed at: 2025-10-03 19:11:00 UTC  
> Url: https://github.com/boostorg/redis/pull/292  



---

## Comment 1

> Username: anarthal  
> Created_at: 2025-07-27 20:58:12 UTC  
> Url: https://github.com/boostorg/redis/pull/292#issuecomment-3124723441  

Still early work. While writing this I've realized that we are in dire need of solving cancellation in `async_exec`. Once you merge #286, I'll start working on that. It should allow us to move the responsibility of cancellation from the tasks in make_parallel_group to run_op.

---
