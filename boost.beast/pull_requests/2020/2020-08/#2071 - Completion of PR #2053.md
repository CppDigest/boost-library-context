# #2071 Completion of PR #2053 [Closed]

> Username: madmongo1  
> Created at: 2020-08-27 12:19:15 UTC  
> Updated at: 2020-08-29 09:39:52 UTC  
> Closed at: 2020-08-29 09:39:52 UTC  
> Url: https://github.com/boostorg/beast/pull/2071  

closes #2053   
  
Adds async handler tracking to all beast async operations.

---

## Comment 1

> Username: madmongo1  
> Created_at: 2020-08-28 07:55:59 UTC  
> Url: https://github.com/boostorg/beast/pull/2071#issuecomment-682385475  

rebased into #2037

---

## Comment 2

> Username: codecov[bot]  
> Created_at: 2020-08-28 11:22:38 UTC  
> Url: https://github.com/boostorg/beast/pull/2071#issuecomment-682471959  

# [Codecov](https://codecov.io/gh/boostorg/beast/pull/2071?src=pr&el=h1) Report  
> Merging [#2071](https://codecov.io/gh/boostorg/beast/pull/2071?src=pr&el=desc) into [develop](https://codecov.io/gh/boostorg/beast/commit/201105e66ab738bd8e027cff8c11ddd7cd5c7587?el=desc) will **increase** coverage by `0.00%`.  
> The diff coverage is `91.39%`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/beast/pull/2071/graphs/tree.svg?width=650&height=150&src=pr&token=Gq6MFA9JRF)](https://codecov.io/gh/boostorg/beast/pull/2071?src=pr&el=tree)  
  
```diff  
@@           Coverage Diff            @@  
##           develop    #2071   +/-   ##  
========================================  
  Coverage    95.09%   95.09%             
========================================  
  Files          153      153             
  Lines        11939    11941    +2       
========================================  
+ Hits         11353    11355    +2       
  Misses         586      586             
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/beast/pull/2071?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [include/boost/beast/core/impl/flat\_stream.hpp](https://codecov.io/gh/boostorg/beast/pull/2071/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9jb3JlL2ltcGwvZmxhdF9zdHJlYW0uaHBw) | `100.00% <ø> (ø)` | |  
| [include/boost/beast/http/impl/read.hpp](https://codecov.io/gh/boostorg/beast/pull/2071/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9odHRwL2ltcGwvcmVhZC5ocHA=) | `98.79% <ø> (ø)` | |  
| [include/boost/beast/http/impl/write.hpp](https://codecov.io/gh/boostorg/beast/pull/2071/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9odHRwL2ltcGwvd3JpdGUuaHBw) | `92.43% <40.00%> (ø)` | |  
| [include/boost/beast/core/detect\_ssl.hpp](https://codecov.io/gh/boostorg/beast/pull/2071/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9jb3JlL2RldGVjdF9zc2wuaHBw) | `93.65% <60.00%> (-1.44%)` | :arrow_down: |  
| [include/boost/beast/core/impl/basic\_stream.hpp](https://codecov.io/gh/boostorg/beast/pull/2071/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9jb3JlL2ltcGwvYmFzaWNfc3RyZWFtLmhwcA==) | `85.87% <66.66%> (ø)` | |  
| [include/boost/beast/websocket/impl/read.hpp](https://codecov.io/gh/boostorg/beast/pull/2071/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC93ZWJzb2NrZXQvaW1wbC9yZWFkLmhwcA==) | `96.68% <91.30%> (ø)` | |  
| [include/boost/beast/websocket/impl/accept.hpp](https://codecov.io/gh/boostorg/beast/pull/2071/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC93ZWJzb2NrZXQvaW1wbC9hY2NlcHQuaHBw) | `100.00% <100.00%> (ø)` | |  
| [include/boost/beast/websocket/impl/close.hpp](https://codecov.io/gh/boostorg/beast/pull/2071/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC93ZWJzb2NrZXQvaW1wbC9jbG9zZS5ocHA=) | `98.81% <100.00%> (ø)` | |  
| [include/boost/beast/websocket/impl/handshake.hpp](https://codecov.io/gh/boostorg/beast/pull/2071/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC93ZWJzb2NrZXQvaW1wbC9oYW5kc2hha2UuaHBw) | `98.26% <100.00%> (ø)` | |  
| [include/boost/beast/websocket/impl/ping.hpp](https://codecov.io/gh/boostorg/beast/pull/2071/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC93ZWJzb2NrZXQvaW1wbC9waW5nLmhwcA==) | `97.32% <100.00%> (+0.89%)` | :arrow_up: |  
| ... and [4 more](https://codecov.io/gh/boostorg/beast/pull/2071/diff?src=pr&el=tree-more) | |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/beast/pull/2071?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/beast/pull/2071?src=pr&el=footer). Last update [201105e...a9574c3](https://codecov.io/gh/boostorg/beast/pull/2071?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---
