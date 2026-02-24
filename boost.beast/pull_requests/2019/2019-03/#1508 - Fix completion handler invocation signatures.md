# #1508 Fix completion handler invocation signatures [Closed]

> Username: djarek  
> Created at: 2019-03-07 09:56:50 UTC  
> Updated at: 2019-09-10 21:03:55 UTC  
> Closed at: 2019-03-10 20:57:54 UTC  
> Url: https://github.com/boostorg/beast/pull/1508  

Completion handlers should be called with the exact same signature as provided to `async_result`.  
  
Fixes: #1506

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2019-03-07 10:15:13 UTC  
> Updated_at: 2019-03-07 10:30:23 UTC  
> Url: https://github.com/boostorg/beast/pull/1508#issuecomment-470467168  

# [Codecov](https://codecov.io/gh/boostorg/beast/pull/1508?src=pr&el=h1) Report  
> Merging [#1508](https://codecov.io/gh/boostorg/beast/pull/1508?src=pr&el=desc) into [develop](https://codecov.io/gh/boostorg/beast/commit/79e6c61db5d8d9ba12ad9d2371d34e4bc5a4b9ff?src=pr&el=desc) will **decrease** coverage by `0.01%`.  
> The diff coverage is `65%`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/beast/pull/1508/graphs/tree.svg?width=650&token=Gq6MFA9JRF&height=150&src=pr)](https://codecov.io/gh/boostorg/beast/pull/1508?src=pr&el=tree)  
  
```diff  
@@             Coverage Diff             @@  
##           develop    #1508      +/-   ##  
===========================================  
- Coverage    92.83%   92.81%   -0.02%       
===========================================  
  Files          149      149                
  Lines        12143    12154      +11       
===========================================  
+ Hits         11273    11281       +8       
- Misses         870      873       +3  
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/beast/pull/1508?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [include/boost/beast/websocket/impl/ping.hpp](https://codecov.io/gh/boostorg/beast/pull/1508/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC93ZWJzb2NrZXQvaW1wbC9waW5nLmhwcA==) | `90.09% <0%> (-0.82%)` | :arrow_down: |  
| [include/boost/beast/websocket/impl/close.hpp](https://codecov.io/gh/boostorg/beast/pull/1508/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC93ZWJzb2NrZXQvaW1wbC9jbG9zZS5ocHA=) | `98.81% <0%> (-0.59%)` | :arrow_down: |  
| [include/boost/beast/websocket/impl/write.hpp](https://codecov.io/gh/boostorg/beast/pull/1508/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC93ZWJzb2NrZXQvaW1wbC93cml0ZS5ocHA=) | `78.96% <0%> (-0.44%)` | :arrow_down: |  
| [include/boost/beast/websocket/impl/handshake.hpp](https://codecov.io/gh/boostorg/beast/pull/1508/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC93ZWJzb2NrZXQvaW1wbC9oYW5kc2hha2UuaHBw) | `98.62% <100%> (ø)` | :arrow_up: |  
| [include/boost/beast/websocket/impl/accept.hpp](https://codecov.io/gh/boostorg/beast/pull/1508/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC93ZWJzb2NrZXQvaW1wbC9hY2NlcHQuaHBw) | `100% <100%> (ø)` | :arrow_up: |  
| [include/boost/beast/websocket/impl/read.hpp](https://codecov.io/gh/boostorg/beast/pull/1508/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC93ZWJzb2NrZXQvaW1wbC9yZWFkLmhwcA==) | `86.51% <100%> (+0.09%)` | :arrow_up: |  
| [include/boost/beast/zlib/detail/deflate\_stream.ipp](https://codecov.io/gh/boostorg/beast/pull/1508/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC96bGliL2RldGFpbC9kZWZsYXRlX3N0cmVhbS5pcHA=) | `80.2% <0%> (+0.1%)` | :arrow_up: |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/beast/pull/1508?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/beast/pull/1508?src=pr&el=footer). Last update [79e6c61...89c6436](https://codecov.io/gh/boostorg/beast/pull/1508?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---

## Comment 2

> Username: djarek  
> Created_at: 2019-03-10 20:57:54 UTC  
> Url: https://github.com/boostorg/beast/pull/1508#issuecomment-471343776  

Merged in 230.

---
