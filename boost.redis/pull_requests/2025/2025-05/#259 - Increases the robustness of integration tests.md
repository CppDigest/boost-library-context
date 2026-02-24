# #259 Increases the robustness of integration tests [Merged]

> Username: anarthal  
> Created at: 2025-05-25 10:11:45 UTC  
> Updated at: 2025-06-06 10:56:49 UTC  
> Merged at: 2025-06-06 10:48:40 UTC  
> Closed at: 2025-06-06 10:48:40 UTC  
> Url: https://github.com/boostorg/redis/pull/259  

Updates multiplexer to make requests complete with `error_code()` rather than `error_code(0)`  
Integration tests now use `io_context::run_for` to run with a timeout and avoid deadlocks  
Tests now use concrete lambdas where generic ones are not required  
Tests now use BOOST_TEST with operator== to print values on error  
Tests now use anonymous namespaces to detect dead code  
Adds run_coroutine_test and removed start from common.hpp  
Updates test_reconnect to perform relevant checks  
Refactors how test_issue_50 launches its coroutines  
Groups tests in CMake as unit or integration  
Updates Jamfile tests to contain all unit tests and only unit tests

---

## Comment 1

> Username: anarthal  
> Created_at: 2025-05-25 10:12:56 UTC  
> Url: https://github.com/boostorg/redis/pull/259#issuecomment-2907736781  

Note that this still needs some work. The idea is that this doesn't change any functionality, but makes the tests more enjoyable. The main point is preventing them from freezing in the CI if something gets broken.

---

## Comment 2

> Username: anarthal  
> Created_at: 2025-06-04 16:04:30 UTC  
> Url: https://github.com/boostorg/redis/pull/259#issuecomment-2940591939  

@mzimbres this is a big PR but entails almost no functional changes - it just sanitizes some aspects of some tests to make them more reliable, use timeouts and so on. It's ready for review.  
  
I'm working in parallel on something that fixes part of the reconnection problems and makes us closer to supporting UNIX sockets.

---

## Review 3 [Commented]

> Username: mzimbres  
> Created_at: 2025-06-05 08:00:18 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/redis/pull/259#pullrequestreview-2899261229  

📁 test/test_conn_echo_stress.cpp

```diff
  81 |    for (auto i = 0; i < n; ++i)
  74 |-       co_await conn->async_exec(*pubs, ignore, net::deferred);
  82 |+       co_await conn.async_exec(pubs, ignore);
```

> Username: mzimbres  
> Created_at: 2025-06-05 08:00:18 UTC  
> Url: https://github.com/boostorg/redis/pull/259#discussion_r2128206513  

`ignore` is the [default response type](https://github.com/boostorg/redis/blob/0c8c6fcc095ff103e9adda708c1efb06fabfe08d/include/boost/redis/connection.hpp#L665) so this could be written more simply as `co_await conn.async_exec(pubs);`  
  
I have just noted that although `ignore` is the default response type in `basic_connection` it isn't in [connection](https://github.com/boostorg/redis/blob/0c8c6fcc095ff103e9adda708c1efb06fabfe08d/include/boost/redis/connection.hpp#L901). I will open an issue to fix this.

> Username: anarthal  
> Created_at: 2025-06-06 10:56:49 UTC  
> Url: https://github.com/boostorg/redis/pull/259#discussion_r2131985198  

Addressed in #265


---
