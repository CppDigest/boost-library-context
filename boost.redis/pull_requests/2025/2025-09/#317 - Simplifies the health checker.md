# #317 Simplifies the health checker [Merged]

> Username: anarthal  
> Created at: 2025-09-26 19:50:48 UTC  
> Updated at: 2025-09-27 17:08:23 UTC  
> Merged at: 2025-09-27 17:08:20 UTC  
> Closed at: 2025-09-27 17:08:20 UTC  
> Url: https://github.com/boostorg/redis/pull/317  

Modifies the health checker to use asio::cancel_after rather than a separate parallel group task

---

## Comment 1

> Username: anarthal  
> Created_at: 2025-09-26 19:53:35 UTC  
> Url: https://github.com/boostorg/redis/pull/317#issuecomment-3340268095  

This PR entails no functional change. It's a simplification of the health checker, following the recent improved cancellation support.  
  
I've written this because I want to rework how cancellation is handled in `async_run` and the parallel group tasks. My aim is making everything use per-operation cancellation, which is the only way to truly avoid race conditions. I don't want to update the old health checker to this new way, since it looks like a waste of time. I also don't wanted to make a super big PR with the cancellation changes, to make it more manageable.

---

## Review 2 [Commented]

> Username: mzimbres  
> Created_at: 2025-09-27 09:25:22 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/redis/pull/317#pullrequestreview-3274758383  

📁 include/boost/redis/connection.hpp

```diff
 331 |+             {
 332 |+                auto* conn = conn_;  // avoid use-after-move problems
 333 |+                auto timeout = 2 * conn->cfg_.health_check_interval;
```

> Username: mzimbres  
> Created_at: 2025-09-27 09:25:22 UTC  
> Url: https://github.com/boostorg/redis/pull/317#discussion_r2384015103  

The `2 *` factor should be removed now to [match the docs](https://github.com/boostorg/redis/blob/f955dc01d2c5b9695f347992c0136321a8ff5276/include/boost/redis/config.hpp#L107-L109).


---

## Review 3 [Commented]

> Username: mzimbres  
> Created_at: 2025-09-27 15:46:32 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/redis/pull/317#pullrequestreview-3274996522  

📁 include/boost/redis/connection.hpp

```diff
 348 |+             // TODO: transforming operation_aborted into pong_timeout
 349 |+             // is unreliable until we migrate everything to use per-operation cancellation.
 350 |+             ec = check_ping_response(ec, conn_->ping_resp_);
```

> Username: mzimbres  
> Created_at: 2025-09-27 15:46:31 UTC  
> Updated_at: 2025-09-27 15:46:32 UTC  
> Url: https://github.com/boostorg/redis/pull/317#discussion_r2384221402  

I think `ec` should not be an argument to `check_ping_response`. It should be treated explicitly above and convert an operation aborted into `pong_timeout` e.g.  
  
```cpp  
if (ec) {  
  assert(ec == operation_canceled); // I guess there should be no other type of errors here?  
  self.complete(pong_timeout);  
}  
  
ec = check_ping_response(conn_->ping_resp_);  
...  
```

> Username: anarthal  
> Created_at: 2025-09-27 16:00:43 UTC  
> Updated_at: 2025-09-27 16:00:44 UTC  
> Url: https://github.com/boostorg/redis/pull/317#discussion_r2384226882  

Not that I can think of, but it's I/O and system calls, so you never know. Best practice says to handle unknown errors, too.

> Username: mzimbres  
> Created_at: 2025-09-27 16:08:24 UTC  
> Updated_at: 2025-09-27 16:16:55 UTC  
> Url: https://github.com/boostorg/redis/pull/317#discussion_r2384229253  

Do you mean something like this?  
```cpp  
if (ec == operation_canceled) {  
  self.complete(pong_timeout);  
} else {  
  self.complete(ec);  
}  
```  
Correction  
  
```cpp  
if (ec == operation_canceled) {  
  self.complete(pong_timeout);  
} else if (!!ec) {  
  self.complete(ec);  
}  
```

> Username: anarthal  
> Created_at: 2025-09-27 16:20:37 UTC  
> Updated_at: 2025-09-27 16:20:39 UTC  
> Url: https://github.com/boostorg/redis/pull/317#discussion_r2384234691  

Yes. I'm refactoring it to a (private) function for sanity.


---

## Review 4 [Commented]

> Username: mzimbres  
> Created_at: 2025-09-27 15:50:40 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/redis/pull/317#pullrequestreview-3274997627  

📁 include/boost/redis/connection.hpp

```diff
 474 |+       system::error_code health_check_ec,
 475 |+       system::error_code reader_ec,
 476 |       system::error_code)
```

> Username: mzimbres  
> Created_at: 2025-09-27 15:50:40 UTC  
> Url: https://github.com/boostorg/redis/pull/317#discussion_r2384222695  

I believe the error omitted here is the `writer_ec`, we should perhaps not omit it for better readability If necessary we can ignore it with `boost::core::ignore_unused`. I am however wondering why it is not handled below.

> Username: anarthal  
> Created_at: 2025-09-27 15:55:24 UTC  
> Updated_at: 2025-09-27 15:55:26 UTC  
> Url: https://github.com/boostorg/redis/pull/317#discussion_r2384225198  

This will be addressed in my next PR on cancellation.


---

## Review 5 [Commented]

> Username: mzimbres  
> Created_at: 2025-09-27 15:57:33 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/redis/pull/317#pullrequestreview-3275000775  

📁 include/boost/redis/impl/ping_request_utils.ipp

```diff
  26 |+    // https://github.com/boostorg/redis/issues/104
  27 |+    if (resp.has_error())
  28 |+       return error::pong_timeout;
```

> Username: mzimbres  
> Created_at: 2025-09-27 15:57:33 UTC  
> Url: https://github.com/boostorg/redis/pull/317#discussion_r2384225852  

AFAICS if `resp` has an error it will be a RESP3 error which should not be converted into a `pong_timeout`. The latter should occur only if `cancel_after` fires. An error in this `resp` object should be returned to the user when the `headl_check` operation completes and its [diagnostic](https://github.com/boostorg/redis/blob/f955dc01d2c5b9695f347992c0136321a8ff5276/include/boost/redis/adapter/result.hpp#L26) should be logged.


---

## Comment 6

> Username: anarthal  
> Created_at: 2025-09-27 16:33:37 UTC  
> Url: https://github.com/boostorg/redis/pull/317#issuecomment-3341903441  

I think I've addressed all of your comments.

---
