# #308 Adds a connection health flag to the multiplexer [Closed]

> Username: anarthal  
> Created at: 2025-09-18 12:22:32 UTC  
> Updated at: 2025-09-19 16:25:32 UTC  
> Closed at: 2025-09-19 16:24:48 UTC  
> Url: https://github.com/boostorg/redis/pull/308  

Uses this flag in async_exec to make cancel_if_not_connected reliable  
Deprecates basic_connection::run_is_canceled()  
Introduces preconditions in multiplexer methods to be called by the reader and writer tasks  
Ensures that request cancellation due to a connection lost is only called after the reader and writer have finished, avoiding potential race conditions  
  
close #236

---

## Comment 1

> Username: anarthal  
> Created_at: 2025-09-18 12:28:40 UTC  
> Url: https://github.com/boostorg/redis/pull/308#issuecomment-3307187457  

The rationale behind this PR is establishing preconditions to multiplexer methods. At the moment, `cancel_on_conn_lost` is called directly from `connection::cancel`, so there is no guarantee with respect to ordering. For instance, the following is possible:  
  
* The reader is parsing a message. More data is needed, so the task gets suspended while parsing.  
* The user calls cancel, so `cancel_on_conn_lost` is called, removing the request we were handling.  
* The reader just receives new data (before getting the cancellation) and invokes `multiplexer::consume_next`, probably yielding a crash.  
  
This is very unlikely, but I'd like to avoid having to test it. With this PR, we guarantee that `cancel_on_conn_lost` will definitely be called after the reader/writer tasks exit.  
  
I've taken the opportunity and added a flag to signal whether our connection is healthy or not, which makes `cancel_if_not_connected` actually do what it advertises.  
  
Let me know your views on this. I can fragment this in 2 PRs if you prefer.

---

## Review 2 [Commented]

> Username: mzimbres  
> Created_at: 2025-09-19 05:17:20 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/redis/pull/308#pullrequestreview-3243079640  

📁 include/boost/redis/connection.hpp

```diff
 177 |    }
 178 | 
 179 |    bool is_open() const noexcept { return stream_.is_open(); }
```

> Username: mzimbres  
> Created_at: 2025-09-19 05:17:20 UTC  
> Url: https://github.com/boostorg/redis/pull/308#discussion_r2361802484  

Can we deprecate `is_open()` as well? Or at least is should be renamed to `is_healthy()`.


---

## Review 3 [Commented]

> Username: mzimbres  
> Created_at: 2025-09-19 05:22:53 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/redis/pull/308#pullrequestreview-3243116928  

📁 include/boost/redis/connection.hpp

```diff
 434 |                clear_response(conn_->setup_resp_);
 435 |+                conn_->read_buffer_.clear();
 436 |+                conn_->mpx_.on_connection_up();
```

> Username: mzimbres  
> Created_at: 2025-09-19 05:22:53 UTC  
> Updated_at: 2025-09-19 05:42:36 UTC  
> Url: https://github.com/boostorg/redis/pull/308#discussion_r2361809090  

I would like to move away the implementation towards https://github.com/boostorg/redis/issues/104. For this PR that would mean `mpx.on_connection_up()` should be actually be called `mpx.log_data_received()` and be called somehow in `redis_stream::async_read_some` when the read size is different from 0. All my other comments in this PR assume this model.  
  
Edit: perhaps it makes more sense to move health funktionality to the redis_sream where data is actually received.

> Username: anarthal  
> Created_at: 2025-09-19 11:22:31 UTC  
> Updated_at: 2025-09-19 11:22:32 UTC  
> Url: https://github.com/boostorg/redis/pull/308#discussion_r2362580634  

This would be equivalent to calling `redis_stream::async_read_some` with `asio::cancel_after(ping_interval)`, and tearing down the connection if the timeout elapses, wouldn't it?


---

## Review 4 [Commented]

> Username: mzimbres  
> Created_at: 2025-09-19 05:25:05 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/redis/pull/308#pullrequestreview-3243125449  

📁 include/boost/redis/detail/multiplexer.hpp

```diff
 123 |+    void on_connection_up();  // Might be called in any state
 124 |+ 
 125 |+    void on_connection_down();  // Must be called once, with is_connection_healthy() == true
```

> Username: mzimbres  
> Created_at: 2025-09-19 05:25:05 UTC  
> Url: https://github.com/boostorg/redis/pull/308#discussion_r2361811782  

This function is not needed since the connection is considered down when data is older than the ping interval specified in `config::health_check_interval`.


---

## Review 5 [Commented]

> Username: mzimbres  
> Created_at: 2025-09-19 05:30:56 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/redis/pull/308#pullrequestreview-3243141959  

📁 include/boost/redis/detail/multiplexer.hpp

```diff
 190 |    usage usage_;
 191 |    any_adapter receive_adapter_;
 192 |+    bool conn_healthy_ = false;
```

> Username: mzimbres  
> Created_at: 2025-09-19 05:30:55 UTC  
> Updated_at: 2025-09-19 05:30:56 UTC  
> Url: https://github.com/boostorg/redis/pull/308#discussion_r2361818866  

I think this could be a `std::chrono::time_point` that gets update at each `log_data_received()`. If creating a timestamp is two expensive i.e. I would need to think more.


---

## Review 6 [Commented]

> Username: mzimbres  
> Created_at: 2025-09-19 05:32:45 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/redis/pull/308#pullrequestreview-3243147090  

📁 include/boost/redis/detail/multiplexer.hpp

```diff
 125 |+    void on_connection_down();  // Must be called once, with is_connection_healthy() == true
 126 |+ 
 127 |+    bool is_connection_healthy() const { return conn_healthy_; }
```

> Username: mzimbres  
> Created_at: 2025-09-19 05:32:45 UTC  
> Updated_at: 2025-09-19 05:32:46 UTC  
> Url: https://github.com/boostorg/redis/pull/308#discussion_r2361821324  

This function should check if conn_healthy_ is not older than `config::health_check_interval`.


---

## Comment 7

> Username: anarthal  
> Created_at: 2025-09-19 10:34:04 UTC  
> Url: https://github.com/boostorg/redis/pull/308#issuecomment-3311665611  

I've clearly tried to get too many things together in a single PR :) I'm going to split this in two: getting rid of the race condition with cancel_on_conn_lost and then improve the health checker. I need to think of your comments about the latter.

---

## Comment 8

> Username: anarthal  
> Created_at: 2025-09-19 16:25:32 UTC  
> Url: https://github.com/boostorg/redis/pull/308#issuecomment-3312851651  

A subset of this PR (including tests) is available in #309. It doesn't include any of the health checking functionality - it only solves the race condition problem.

---
