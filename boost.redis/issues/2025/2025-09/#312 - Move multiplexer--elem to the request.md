# #312 - Move multiplexer::elem to the request [Closed]

> Username: mzimbres  
> Created at: 2025-09-21 20:54:18 UTC  
> Updated at: 2025-11-19 15:16:58 UTC  
> Closed at: 2025-11-19 09:48:36 UTC  
> Url: https://github.com/boostorg/redis/issues/312  

I think there is no reason to keep `multiplexer::elem` in the `multiplexer` instead of the `request`. Moving it would also eliminate one dynamic allocation from each `async_exec` helping to reduce latency even further.

---

## Comment 1

> Username: anarthal  
> Created at: 2025-09-21 21:11:56 UTC  
> Url: https://github.com/boostorg/redis/issues/312#issuecomment-3316266351  

There is, see #310. A cancelled request is implemented there as an elem without an attached request.

---

## Comment 2

> Username: mzimbres  
> Created at: 2025-09-21 21:18:19 UTC  
> Url: https://github.com/boostorg/redis/issues/312#issuecomment-3316269642  

I have to look in more detail but I guess we could avoid that dynamic allocation somehow.

---

## Comment 3

> Username: anarthal  
> Created at: 2025-09-21 21:19:55 UTC  
> Url: https://github.com/boostorg/redis/issues/312#issuecomment-3316270511  

I'm open to different implementations though. I think ee could replace the deque and shared_ptrs by linked lists, possibly using Boost.Intrusive. It would save the overhead of atomic counters, and the repeated partitioning in the multiplexer (which causes allocations too). But it means introducing a dependency which can't be hidden in .ipps.

---

## Comment 4

> Username: mzimbres  
> Created at: 2025-11-19 09:48:36 UTC  
> Url: https://github.com/boostorg/redis/issues/312#issuecomment-3551797169  

> Move multiplexer::elem to the request  
  
I think this is not a good idea after all as it would prevent the request from being passed as a `const` parameter. I think what your proposal elsewhere about allocating only once for the `notifier` and the `elem` is indeed better https://github.com/boostorg/redis/blob/00f3ec9b78454211c7b8d461b11411c8b1ef1ad3/include/boost/redis/connection.hpp#L193-L194  
  
Once that is done we can implement caching of these structures to get asymptotically zero allocations. I will close this ticket and open a new one.

---

## Comment 5

> Username: anarthal  
> Created at: 2025-11-19 15:16:58 UTC  
> Url: https://github.com/boostorg/redis/issues/312#issuecomment-3553285352  

Agree.
