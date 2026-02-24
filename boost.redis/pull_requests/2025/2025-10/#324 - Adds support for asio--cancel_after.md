# #324 Adds support for asio::cancel_after [Merged]

> Username: anarthal  
> Created at: 2025-10-03 19:06:38 UTC  
> Updated at: 2025-10-06 16:11:28 UTC  
> Merged at: 2025-10-06 16:11:25 UTC  
> Closed at: 2025-10-06 16:11:25 UTC  
> Url: https://github.com/boostorg/redis/pull/324  

* Adds support for asio::cancel_after in connection::{async_exec, async_run}  
* Adds cancel_after tests  
* Adds an example on using asio::cancel_after  
* Adds a discussion page on timeouts and the `cancel_if_unresponded` flag  
  
close #226

---

## Comment 1

> Username: anarthal  
> Created_at: 2025-10-03 19:08:32 UTC  
> Url: https://github.com/boostorg/redis/pull/324#issuecomment-3366891511  

This may still yield CI errors due to the race conditions fixed by #320, so this one should be merged before this.

---

## Review 2 [Commented]

> Username: mzimbres  
> Created_at: 2025-10-04 15:15:30 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/redis/pull/324#pullrequestreview-3301942320  

📁 doc/modules/ROOT/pages/cancellation.adoc

```diff
  33 |+ In this situation, we don't know if the request was processed by the server or not.
  34 |+ And we have no way to know it. By default, the library mark these requests as
  35 |+ failed with `asio::error::operation_aborted`. (TODO: do we want another error code here?).
```

> Username: mzimbres  
> Created_at: 2025-10-04 15:15:30 UTC  
> Url: https://github.com/boostorg/redis/pull/324#discussion_r2404030631  

> the library mark these  
  
Needs an **s** at the end.  
  
> do we want another error code here?  
  
I think we do, otherwise the user might not know why it was canceled. Transforming `operation_aborted` into e.g. `unresponded_on_connection_lost` will make it clear.

> Username: anarthal  
> Created_at: 2025-10-06 15:30:17 UTC  
> Updated_at: 2025-10-06 15:30:18 UTC  
> Url: https://github.com/boostorg/redis/pull/324#discussion_r2407034145  

Opened #326 to track this.


---

## Review 3 [Commented]

> Username: mzimbres  
> Created_at: 2025-10-04 15:25:14 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/redis/pull/324#pullrequestreview-3301957157  

📁 doc/modules/ROOT/pages/cancellation.adoc

```diff
  51 |+ 
  52 |+ // Makes sure that the key is set, even in the presence of network errors.
  53 |+ // This may suspend for an unspecified period of time if the server is down.
```

> Username: mzimbres  
> Created_at: 2025-10-04 15:25:14 UTC  
> Url: https://github.com/boostorg/redis/pull/324#discussion_r2404044314  

> This may suspend for an unspecified period of time if the server is down.  
  
I would rephrase this to something like  
  
> This will remain suspended until the server is capable of serving requests again e.g. after a process manager restarted the server.


---

## Review 4 [Commented]

> Username: mzimbres  
> Created_at: 2025-10-04 15:31:22 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/redis/pull/324#pullrequestreview-3301958940  

📁 include/boost/redis/connection.hpp

```diff
1351 | private:
1352 |+    // Function object to initiate the async ops that use asio::any_completion_handler.
1353 |+    // Required for asio::cancel_after to work.
```

> Username: mzimbres  
> Created_at: 2025-10-04 15:31:22 UTC  
> Url: https://github.com/boostorg/redis/pull/324#discussion_r2404046063  

Why is this required? i.e. why wouldn't the lambda be enough?

> Username: anarthal  
> Created_at: 2025-10-04 20:13:30 UTC  
> Url: https://github.com/boostorg/redis/pull/324#discussion_r2404154335  

cancel_after (without a timer argument) requires an executor to construct a temporary timer for the timeout. This executor is taken from the initiation object. This gets done for free if you use async_compose, but needs to be done manually when using async_initiate.


---
