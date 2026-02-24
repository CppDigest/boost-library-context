# #381 Makes async_receive2 not cancel on reconnection [Merged]

> Username: anarthal  
> Created at: 2026-01-16 13:04:07 UTC  
> Updated at: 2026-01-18 15:14:57 UTC  
> Merged at: 2026-01-18 15:14:54 UTC  
> Closed at: 2026-01-18 15:14:54 UTC  
> Url: https://github.com/boostorg/redis/pull/381  

async_receive2 is now only cancelled after calling connection::cancel() or using per-operation cancellation  
Adds a restriction to only have one outstanding async_receive2 operation per connection  
Adds error::already_running  
Adds support for asio::cancel_after for async_receive2  
Deprecates cancel(operation::receive)  
Adds more documentation to async_receive2  
  
close #331

---

## Comment 1

> Username: anarthal  
> Created_at: 2026-01-16 13:04:29 UTC  
> Url: https://github.com/boostorg/redis/pull/381#issuecomment-3759941800  

Still lacking unit tests for the FSM.

---

## Review 2 [Commented]

> Username: mzimbres  
> Created_at: 2026-01-17 19:25:57 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/redis/pull/381#pullrequestreview-3674345037  

📁 include/boost/redis/connection.hpp

```diff
 830 |+     *     This can be inefficient when receiving lots of server pushes.
 831 |+     *     `async_receive2` is batch-oriented, and will only suspend once if
 832 |+     *     several pushes are received in a single network packet.
```

> Username: mzimbres  
> Created_at: 2026-01-17 19:25:57 UTC  
> Url: https://github.com/boostorg/redis/pull/381#discussion_r2701372290  

```diff  
-    *     several pushes are received in a single network packet.  
+    *     256 push messages accumulate internally.  
```

> Username: anarthal  
> Created_at: 2026-01-18 13:10:36 UTC  
> Url: https://github.com/boostorg/redis/pull/381#discussion_r2702402116  

Hmmm, no. The reader suspends if 256 push messages accumulate. `async_receive2` is the consumer side. I'll try to rephrase this though.


---
