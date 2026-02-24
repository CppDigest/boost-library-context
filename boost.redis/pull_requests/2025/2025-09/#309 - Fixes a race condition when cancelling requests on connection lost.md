# #309 Fixes a race condition when cancelling requests on connection lost [Merged]

> Username: anarthal  
> Created at: 2025-09-19 16:24:33 UTC  
> Updated at: 2025-09-22 11:04:32 UTC  
> Merged at: 2025-09-22 11:04:29 UTC  
> Closed at: 2025-09-22 11:04:29 UTC  
> Url: https://github.com/boostorg/redis/pull/309  

* Changes how cancel_on_conn_lost is used to ensure it is called only once and after the reader and writer tasks have exited.  
* This fixes a problem in test_conn_reconnect  
* Adds a test for multiplexer::reset()  
* Adds stronger invariants to the multiplexer functions to be called by the reader and writer  
* Removes test_issue_181, since the same functionality is being covered by unit tests already  
* Removes basic_connection::run_is_canceled

---

## Review 1 [Commented]

> Username: mzimbres  
> Created_at: 2025-09-20 13:54:23 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/redis/pull/309#pullrequestreview-3249049945  

📁 include/boost/redis/impl/multiplexer.ipp

```diff
 209 |+    // See https://github.com/boostorg/redis/issues/181
 210 |+    BOOST_ASSERT(!cancel_run_called_);
 211 |+    cancel_run_called_ = true;
```

> Username: mzimbres  
> Created_at: 2025-09-20 13:54:23 UTC  
> Url: https://github.com/boostorg/redis/pull/309#discussion_r2365638310  

This flag is now used only in asserts? Can we protect its declaration around `#if DEBUG ... #endif`?

> Username: anarthal  
> Created_at: 2025-09-22 10:16:35 UTC  
> Url: https://github.com/boostorg/redis/pull/309#discussion_r2367451447  

I'm not too fan of changing class layout like this, as IIRC could cause trouble (e.g. when mixing translation units with different compile flags). I think we will get rid of this flag once we review the health checker, and it's still one byte per connection, so I'd say leave as is for now.


---

## Review 2 [Commented]

> Username: mzimbres  
> Created_at: 2025-09-20 14:01:36 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/redis/pull/309#pullrequestreview-3249051639  

📁 include/boost/redis/detail/multiplexer.hpp

```diff
 120 |+    // To be called by the reader and the writer. Might only be called
 121 |+    // before the connection is torn down (before cancel_on_conn_lost() is called).
 122 |+    //
```

> Username: mzimbres  
> Created_at: 2025-09-20 14:01:36 UTC  
> Url: https://github.com/boostorg/redis/pull/309#discussion_r2365640372  

This comment looks misplaced. I don't get why prepare_write can only be called by the reader and writer and only before the connection is torn down. It is called on every write operation by the writer.

> Username: anarthal  
> Created_at: 2025-09-22 10:22:20 UTC  
> Url: https://github.com/boostorg/redis/pull/309#discussion_r2367484276  

It's necessary for `cancel_on_conn_lost()` to establish its invariants. The idea is that, after `cancel_on_conn_lost()` is called, all requests are either cancelled or return to the waiting state, so they can be re-sent afterwards. If, for whatever reason, a race condition happens and cancel_on_conn_lost() runs first, and then a `prepare_write()` happens (because the writer hasn't exited yet), then there will be leftover requests in `staged` state. When the connection is re-established, these won't be sent to the server again (since they are `staged`), but there won't be any response coming (since the write happened in the previous connection), causing trouble.  
  
By making sure that all operations that might "progress" a request happen before `cancel_on_conn_lost()` is called, we can make sure that `cancel_on_conn_lost()` leaves everything in a well-defined state.  
  
That said, the comment should probably express what I've written here, rather than what it says today. I'll update it.


---
