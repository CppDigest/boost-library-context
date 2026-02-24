# #321 Adds per-operation cancellation support to async_run and cleans up cancellation [Merged]

> Username: anarthal  
> Created at: 2025-10-03 09:48:52 UTC  
> Updated at: 2025-10-03 16:48:33 UTC  
> Merged at: 2025-10-03 16:48:30 UTC  
> Closed at: 2025-10-03 16:48:30 UTC  
> Url: https://github.com/boostorg/redis/pull/321  

* Adds support for terminal and partial cancellation to async_run.  
* Makes basic_connection::cancel use per-operation cancellation under the hood.  
* Fixes a number of race conditions during cancellation which could cause the cancellation to be ignored. This could happen if the cancellation is delivered while an async handler is pending execution.  
* Deprecates operation::{resolve, connect, ssl_handshake, reconnection, health_check}, in favor of operation::run. Calling basic_connection::cancel with these values (excepting reconnection) is now equivalent to using operation::run.  
* Fixes a problem in the health checker that caused ping timeouts to be reported as cancellations.  
* Sanitizes how the parallel group computes its final error code.  
* Simplifies the reader, writer and health checker to not care about connection cancellation. This is now the responsibility of the parallel group.  
* Removes an unnecessary setup_cancellation action in the reader FSM.  
* Adds documentation regarding per-operation cancellation to async_receive.  
* Adds additional health checker tests.  
* Adds async_run per-operation cancellation tests.  
* Adds reader FSM cancellation tests.  
* Makes test_conn_exec_retry tests more resilient.  
* Removes leftovers in the UNIX and TLS reconnection tests. These were required due to race conditions that have already been fixed.  
  
close #318   
close #319

---

## Comment 1

> Username: anarthal  
> Created_at: 2025-10-03 10:07:55 UTC  
> Url: https://github.com/boostorg/redis/pull/321#issuecomment-3365127900  

This is a big rework on how cancellation is handled in the connection. It has the side effect of implementing per-operation cancellation for async_run, but the main point is fixing race conditions and making the reader/writer not concerned about cancelling the connection.  
  
I prefer merging this before #320, which fixes the last race conditions regarding cancellation. Once this is done, I'll submit a PR implementing support for cancel_after in connection.

---

## Review 2 [Commented]

> Username: mzimbres  
> Created_at: 2025-10-03 11:29:43 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/redis/pull/321#pullrequestreview-3298539850  

📁 include/boost/redis/connection.hpp

```diff
 355 |+ 
 356 |+             // Wait until we're cancelled. This simplifies parallel group handling a lot
 357 |+             conn_->ping_timer_.expires_at((std::chrono::steady_clock::time_point::max)());
```

> Username: mzimbres  
> Created_at: 2025-10-03 11:29:43 UTC  
> Url: https://github.com/boostorg/redis/pull/321#discussion_r2401588452  

This is a good idea. But I guess it would have been even better to disable health-checks by letting users set `max()` instead of `zero()`, then we would not need this branch at all. We would only have to reformulate the loop below to first wait and then to ping and not the other way around. That would be a breaking change hard to get around. We would have to rename `health_check_interval` to something like `health_check_interval2` to intentionally break user code.

> Username: anarthal  
> Created_at: 2025-10-03 11:48:50 UTC  
> Updated_at: 2025-10-03 11:48:51 UTC  
> Url: https://github.com/boostorg/redis/pull/321#discussion_r2401626509  

Hm, that wouldn't work because the interval is a duration, not a time_point. I actually had a bug at some point where I used `duration::max` rather than `time_point::max` and it caused an overflow error, with the operation finishing immediately.


---

## Review 3 [Commented]

> Username: mzimbres  
> Created_at: 2025-10-03 11:34:49 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/redis/pull/321#pullrequestreview-3298552080  

📁 include/boost/redis/connection.hpp

```diff
 417 | }
 418 | 
 419 |+ inline system::error_code translate_parallel_group_errors(
```

> Username: mzimbres  
> Created_at: 2025-10-03 11:34:49 UTC  
> Url: https://github.com/boostorg/redis/pull/321#discussion_r2401597714  

I know `inline` functions might result in better performance but for this specific case it won't make a difference. I want to make compilation as fast as possible, so I prefer to move this to an `.ipp` file.


---

## Review 4 [Commented]

> Username: mzimbres  
> Created_at: 2025-10-03 11:56:47 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/redis/pull/321#pullrequestreview-3298608152  

📁 include/boost/redis/connection.hpp

```diff
 814 |+       if (slot.is_connected()) {
 815 |+          slot.template emplace<detail::run_cancel_handler<Executor>>(*impl_);
 816 |+       }
```

> Username: mzimbres  
> Created_at: 2025-10-03 11:56:47 UTC  
> Url: https://github.com/boostorg/redis/pull/321#discussion_r2401643263  

IIUC this code requires the user slot to not outlive the connection i.e. users should not emit a signal on it if the connection ceased to exist. I am not sure but some people might think this would be a noop instead of crash, so perhaps a note on the docs?

> Username: anarthal  
> Created_at: 2025-10-03 14:21:11 UTC  
> Updated_at: 2025-10-03 14:21:13 UTC  
> Url: https://github.com/boostorg/redis/pull/321#discussion_r2402127281  

Actually, this is what happens with all Asio objects. I also thought that it should be a noop (and went down a very hard path to achieve this in MySQL). And yet a single timer doesn't handle this. I've just checked with ASAN, and emitting a cancel signal on a timer after it's been destroyed accesses freed memory.  
  
In any case, I think you're right so I'm gonna document it, at least.


---

## Review 5 [Commented]

> Username: mzimbres  
> Created_at: 2025-10-03 11:59:14 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/redis/pull/321#pullrequestreview-3298619502  

📁 include/boost/redis/connection.hpp

```diff
  86 |    timer_type ping_timer_;       // to wait between pings
  87 |    receive_channel_type receive_channel_;
  88 |+    asio::cancellation_signal run_signal_;
```

> Username: mzimbres  
> Created_at: 2025-10-03 11:59:13 UTC  
> Updated_at: 2025-10-03 11:59:14 UTC  
> Url: https://github.com/boostorg/redis/pull/321#discussion_r2401656119  

Would this still be needed if we remove the cancel function a let would work entirely with user provided `cancellation_signals`?

> Username: anarthal  
> Created_at: 2025-10-03 14:27:56 UTC  
> Updated_at: 2025-10-03 14:27:57 UTC  
> Url: https://github.com/boostorg/redis/pull/321#discussion_r2402158254  

No, it wouldn't. It only exists to keep compatibility with `cancel()`. But I feel removing it is breaking too much (essentially, everyone).


---

## Comment 6

> Username: mzimbres  
> Created_at: 2025-10-03 12:12:50 UTC  
> Url: https://github.com/boostorg/redis/pull/321#issuecomment-3365455462  

Some many nice things. The simplifications on the reader_fsm are relieving. Good job, thanks.

---
