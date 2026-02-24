# #1919 Fix async_detect_ssl for net::use_awaitable  [Closed]

> Username: madmongo1  
> Created at: 2020-04-27 14:23:41 UTC  
> Updated at: 2020-04-28 18:25:15 UTC  
> Closed at: 2020-04-28 18:25:15 UTC  
> Url: https://github.com/boostorg/beast/pull/1919  

fixes #1918

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2020-04-27 15:16:13 UTC  
> Updated_at: 2020-04-27 19:04:51 UTC  
> Url: https://github.com/boostorg/beast/pull/1919#issuecomment-620050754  

# [Codecov](https://codecov.io/gh/boostorg/beast/pull/1919?src=pr&el=h1) Report  
> Merging [#1919](https://codecov.io/gh/boostorg/beast/pull/1919?src=pr&el=desc) into [develop](https://codecov.io/gh/boostorg/beast/commit/f5064e0c460664fa34fdad44e2379a2309d39685&el=desc) will **decrease** coverage by `0.00%`.  
> The diff coverage is `100.00%`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/beast/pull/1919/graphs/tree.svg?width=650&height=150&src=pr&token=Gq6MFA9JRF)](https://codecov.io/gh/boostorg/beast/pull/1919?src=pr&el=tree)  
  
```diff  
@@             Coverage Diff             @@  
##           develop    #1919      +/-   ##  
===========================================  
- Coverage    95.13%   95.12%   -0.01%       
===========================================  
  Files          156      156                
  Lines        12032    12032                
===========================================  
- Hits         11447    11446       -1       
- Misses         585      586       +1       
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/beast/pull/1919?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [include/boost/beast/core/detect\_ssl.hpp](https://codecov.io/gh/boostorg/beast/pull/1919/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9jb3JlL2RldGVjdF9zc2wuaHBw) | `95.08% <100.00%> (ø)` | |  
| [include/boost/beast/websocket/impl/ping.hpp](https://codecov.io/gh/boostorg/beast/pull/1919/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC93ZWJzb2NrZXQvaW1wbC9waW5nLmhwcA==) | `96.42% <0.00%> (-0.90%)` | :arrow_down: |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/beast/pull/1919?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/beast/pull/1919?src=pr&el=footer). Last update [f5064e0...c8bf6fd](https://codecov.io/gh/boostorg/beast/pull/1919?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---

## Review 2 [Commented]

> Username: vinniefalco  
> Created_at: 2020-04-27 15:19:12 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/1919#pullrequestreview-401057775  

📁 test/beast/core/detect_ssl.cpp

```diff
 201 |+         }
 202 |+ 
 203 |+         // true
```

> Username: vinniefalco  
> Created_at: 2020-04-27 15:19:12 UTC  
> Updated_at: 2020-04-27 18:34:44 UTC  
> Url: https://github.com/boostorg/beast/pull/1919#discussion_r415904439  

Do we need all these extra tests? It should be enough just to check that it compiles and runs the algorithm.


---

## Review 3 [Commented]

> Username: vinniefalco  
> Created_at: 2020-04-27 15:22:33 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/1919#pullrequestreview-401060915  

📁 test/beast/core/detect_ssl.cpp

```diff
 173 | 
 174 |+ #if BOOST_ASIO_HAS_CO_AWAIT
 175 |+     void testAsyncReadCoro()
```

> Username: vinniefalco  
> Created_at: 2020-04-27 15:22:33 UTC  
> Updated_at: 2020-04-27 18:34:44 UTC  
> Url: https://github.com/boostorg/beast/pull/1919#discussion_r415907190  

I think this test should just instantiate the initiating function with a `net::use_awaitable` (for example, by taking the address of it)

> Username: madmongo1  
> Created_at: 2020-04-27 15:53:54 UTC  
> Updated_at: 2020-04-27 18:34:44 UTC  
> Url: https://github.com/boostorg/beast/pull/1919#discussion_r415935415  

pdimov  17:33  
the test tests exactly what it needs - the actual "customer" scenario  
17:34  
no sane user just takes the address of the function


---
