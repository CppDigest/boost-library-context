# #1997 Support BOOST_ASIO_NO_TS_EXECUTORS [Closed]

> Username: madmongo1  
> Created at: 2020-06-25 14:05:35 UTC  
> Updated at: 2020-07-02 15:03:18 UTC  
> Closed at: 2020-07-02 15:03:09 UTC  
> Url: https://github.com/boostorg/beast/pull/1997  

Placeholder PR for testing fixes to the the NO_TS_EXECUTORS build mode.

---

## Review 1 [Commented]

> Username: vinniefalco  
> Created_at: 2020-06-25 14:07:58 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/1997#pullrequestreview-437530593  

📁 include/boost/beast/core/async_base.hpp

```diff
 199 |+         net::associated_executor<
 200 |+             Handler,
 201 |+             typename detail::select_work_guard_t<Executor1>::executor_type
```

> Username: vinniefalco  
> Created_at: 2020-06-25 14:07:57 UTC  
> Updated_at: 2020-06-30 16:47:45 UTC  
> Url: https://github.com/boostorg/beast/pull/1997#discussion_r445586053  

This is kind of annoying, a `detail` type will show up in the docs.

> Username: madmongo1  
> Created_at: 2020-06-25 14:08:37 UTC  
> Updated_at: 2020-06-30 16:47:45 UTC  
> Url: https://github.com/boostorg/beast/pull/1997#discussion_r445586553  

we could replace it with a __deduced__ in the docs?

> Username: vinniefalco  
> Created_at: 2020-06-25 14:09:20 UTC  
> Updated_at: 2020-06-30 16:47:45 UTC  
> Url: https://github.com/boostorg/beast/pull/1997#discussion_r445587062  

You might be able to say this instead:  
```  
#ifdef BOOST_BEAST_DOCS  
    using executor_type = typename  
        net::associated_executor<  
            Handler,  
            __implementation_defined__>::type  
#else  
  ...  
#endif  
```  
  
Or just  
  
```  
using executor_type = __see_below__;  
```

> Username: vinniefalco  
> Created_at: 2020-06-25 14:44:51 UTC  
> Updated_at: 2020-06-30 16:47:45 UTC  
> Url: https://github.com/boostorg/beast/pull/1997#discussion_r445613570  

yeah deduced might work, check the main.qbk for the exact spelling


---

## Review 2 [Commented]

> Username: vinniefalco  
> Created_at: 2020-06-25 14:08:16 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/1997#pullrequestreview-437530877  

📁 include/boost/beast/core/async_base.hpp

```diff
 246 |         : h_(std::forward<Handler_>(handler))
 232 |-         , wg1_(ex1)
 247 |+         , wg1_(detail::make_work_guard(ex1))
```

> Username: vinniefalco  
> Created_at: 2020-06-25 14:08:16 UTC  
> Updated_at: 2020-06-30 16:47:45 UTC  
> Url: https://github.com/boostorg/beast/pull/1997#discussion_r445586253  

Should this be called `make_tracked`?

> Username: madmongo1  
> Created_at: 2020-06-25 14:10:00 UTC  
> Updated_at: 2020-06-30 16:47:45 UTC  
> Url: https://github.com/boostorg/beast/pull/1997#discussion_r445587499  

I don't know whether the net.ts or the execution nightmare is more likely to win out in future. I called it something I felt we would both woud understand.

> Username: vinniefalco  
> Created_at: 2020-06-25 14:45:14 UTC  
> Updated_at: 2020-06-30 16:47:45 UTC  
> Url: https://github.com/boostorg/beast/pull/1997#discussion_r445613861  

fair, and its in detail so users won't/shouldn't see it.


---

## Review 3 [Commented]

> Username: vinniefalco  
> Created_at: 2020-06-25 14:10:58 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/1997#pullrequestreview-437533513  

📁 test/beast/core/async_base.cpp

```diff
  44 |+     net::execution::blocking_t
  45 |+     query(net::execution::blocking_t) const noexcept
  46 |+     { return net::execution::blocking; };
```

> Username: vinniefalco  
> Created_at: 2020-06-25 14:10:58 UTC  
> Updated_at: 2020-06-30 16:47:45 UTC  
> Url: https://github.com/boostorg/beast/pull/1997#discussion_r445588172  

Please, separate lines:  
```  
    net::execution::blocking_t  
    query(net::execution::blocking_t) const noexcept  
    {  
        return net::execution::blocking;  
    };  
```


---

## Review 4 [Commented]

> Username: vinniefalco  
> Created_at: 2020-06-25 14:11:11 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/1997#pullrequestreview-437533715  

📁 test/beast/core/async_base.cpp

```diff
  78 |+     { return false; }
  79 |+ };
  80 |+ static_assert(net::execution::can_execute<ex1_type, net::execution::invocable_archetype>::value, "");
```

> Username: vinniefalco  
> Created_at: 2020-06-25 14:11:10 UTC  
> Updated_at: 2020-06-30 16:47:45 UTC  
> Url: https://github.com/boostorg/beast/pull/1997#discussion_r445588318  

`BOOST_STATIC_ASSERT`?


---

## Review 5 [Commented]

> Username: vinniefalco  
> Created_at: 2020-06-25 21:03:12 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/1997#pullrequestreview-437856675  

📁 example/http/client/coro/http_client_coro.cpp

```diff
 149 |+ {
 150 |+ }
 151 |+ #endif
```

> Username: vinniefalco  
> Created_at: 2020-06-25 21:03:11 UTC  
> Updated_at: 2020-06-30 16:47:45 UTC  
> Url: https://github.com/boostorg/beast/pull/1997#discussion_r445836638  

missing newline


---

## Comment 6

> Username: codecov[bot]  
> Created_at: 2020-06-26 01:25:11 UTC  
> Updated_at: 2020-06-30 17:22:11 UTC  
> Url: https://github.com/boostorg/beast/pull/1997#issuecomment-649897319  

# [Codecov](https://codecov.io/gh/boostorg/beast/pull/1997?src=pr&el=h1) Report  
> Merging [#1997](https://codecov.io/gh/boostorg/beast/pull/1997?src=pr&el=desc) into [develop](https://codecov.io/gh/boostorg/beast/commit/6f57e5934c1f307e4510ee11dd09594526a3f5d0&el=desc) will **decrease** coverage by `0.01%`.  
> The diff coverage is `100.00%`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/beast/pull/1997/graphs/tree.svg?width=650&height=150&src=pr&token=Gq6MFA9JRF)](https://codecov.io/gh/boostorg/beast/pull/1997?src=pr&el=tree)  
  
```diff  
@@             Coverage Diff             @@  
##           develop    #1997      +/-   ##  
===========================================  
- Coverage    95.10%   95.09%   -0.02%       
===========================================  
  Files          152      153       +1       
  Lines        11960    11934      -26       
===========================================  
- Hits         11375    11349      -26       
  Misses         585      585                
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/beast/pull/1997?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [include/boost/beast/core/basic\_stream.hpp](https://codecov.io/gh/boostorg/beast/pull/1997/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9jb3JlL2Jhc2ljX3N0cmVhbS5ocHA=) | `100.00% <ø> (ø)` | |  
| [include/boost/beast/core/detail/bind\_handler.hpp](https://codecov.io/gh/boostorg/beast/pull/1997/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9jb3JlL2RldGFpbC9iaW5kX2hhbmRsZXIuaHBw) | `98.03% <ø> (-1.97%)` | :arrow_down: |  
| [include/boost/beast/core/impl/async\_base.hpp](https://codecov.io/gh/boostorg/beast/pull/1997/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9jb3JlL2ltcGwvYXN5bmNfYmFzZS5ocHA=) | `100.00% <ø> (ø)` | |  
| [include/boost/beast/core/impl/saved\_handler.hpp](https://codecov.io/gh/boostorg/beast/pull/1997/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9jb3JlL2ltcGwvc2F2ZWRfaGFuZGxlci5ocHA=) | `100.00% <ø> (ø)` | |  
| [include/boost/beast/websocket/stream.hpp](https://codecov.io/gh/boostorg/beast/pull/1997/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC93ZWJzb2NrZXQvc3RyZWFtLmhwcA==) | `100.00% <ø> (ø)` | |  
| [include/boost/beast/core/async\_base.hpp](https://codecov.io/gh/boostorg/beast/pull/1997/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9jb3JlL2FzeW5jX2Jhc2UuaHBw) | `100.00% <100.00%> (ø)` | |  
| [include/boost/beast/core/detail/get\_io\_context.hpp](https://codecov.io/gh/boostorg/beast/pull/1997/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9jb3JlL2RldGFpbC9nZXRfaW9fY29udGV4dC5ocHA=) | `94.73% <100.00%> (-0.27%)` | :arrow_down: |  
| [include/boost/beast/core/detail/work\_guard.hpp](https://codecov.io/gh/boostorg/beast/pull/1997/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9jb3JlL2RldGFpbC93b3JrX2d1YXJkLmhwcA==) | `100.00% <100.00%> (ø)` | |  
| [include/boost/beast/websocket/impl/stream\_impl.hpp](https://codecov.io/gh/boostorg/beast/pull/1997/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC93ZWJzb2NrZXQvaW1wbC9zdHJlYW1faW1wbC5ocHA=) | `96.86% <100.00%> (ø)` | |  
| ... and [3 more](https://codecov.io/gh/boostorg/beast/pull/1997/diff?src=pr&el=tree-more) | |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/beast/pull/1997?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/beast/pull/1997?src=pr&el=footer). Last update [6f57e59...bd52e73](https://codecov.io/gh/boostorg/beast/pull/1997?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---

## Review 7 [Commented]

> Username: vinniefalco  
> Created_at: 2020-06-30 16:57:26 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/1997#pullrequestreview-440212481  

📁 test/doc/core_3_timeouts.cpp

```diff
 285 | 
 286 |- #if BOOST_BEAST_ENABLE_STACKFUL
 286 |+ #if BOOST_BEAST_ENABLE_STACKFUL_TESTS
```

> Username: vinniefalco  
> Created_at: 2020-06-30 16:57:26 UTC  
> Url: https://github.com/boostorg/beast/pull/1997#discussion_r447837851  

If you leave this in, it will mess up the docs. Suggested:  
```  
#if defined(BOOST_BEAST_DOCS) || defined(BOOST_BEAST_ENABLE_STACKFUL_TESTS)  
```  
  
(The macro might be `BOOST_BEAST_DOXYGEN`, I can't recall)

> Username: madmongo1  
> Created_at: 2020-06-30 16:58:56 UTC  
> Updated_at: 2020-06-30 16:58:57 UTC  
> Url: https://github.com/boostorg/beast/pull/1997#discussion_r447838859  

I should be able to remove it completely now


---
