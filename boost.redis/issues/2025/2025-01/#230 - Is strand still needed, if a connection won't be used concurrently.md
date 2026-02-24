# #230 - Is strand still needed, if a connection won't be used concurrently. [Closed]

> Username: feverzsj  
> Created at: 2025-01-20 17:03:30 UTC  
> Updated at: 2025-01-22 12:08:55 UTC  
> Closed at: 2025-01-22 12:08:55 UTC  
> Url: https://github.com/boostorg/redis/issues/230  

For example, a connection is only used inside a single coroutine spawned on a thread pool.

---

## Comment 1

> Username: criatura2  
> Created at: 2025-01-22 09:36:20 UTC  
> Url: https://github.com/boostorg/redis/issues/230#issuecomment-2606729636  

Hi. Yes, it is necessary because the connection will spawn multiple operation on its executor, if that executor is multithreaded races will arise.
