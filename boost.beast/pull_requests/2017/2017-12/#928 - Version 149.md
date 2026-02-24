# #928 Version 149 [Merged]

> Username: vinniefalco  
> Created at: 2017-12-08 18:59:35 UTC  
> Updated at: 2018-01-04 09:23:31 UTC  
> Merged at: 2017-12-09 22:25:58 UTC  
> Closed at: 2017-12-09 22:25:58 UTC  
> Url: https://github.com/boostorg/beast/pull/928  

* built-in r-value return values can't be assigned  
* Tidy up ssl_stream special members  
* Fix CMakeLists.txt variable  
* Protect calls from macros  
* pausation always allocates  
* Don't copy completion handlers  
* handler_ptr is move-only  
  
API Changes:  
  
* handler_ptr gives the strong exception guarantee  
  
Actions Required:  
  
* Change the constructor signature for state objects  
  used with handler_ptr to receive a const reference to  
  the handler.

---

## Review 1 [Approved]

> Username: djarek  
> Created_at: 2017-12-08 19:46:29 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/beast/pull/928#pullrequestreview-82255198  

LGTM

---

## Comment 2

> Username: codecov[bot]  
> Created_at: 2017-12-08 20:04:15 UTC  
> Updated_at: 2017-12-09 21:25:01 UTC  
> Url: https://github.com/boostorg/beast/pull/928#issuecomment-350359055  

# [Codecov](https://codecov.io/gh/boostorg/beast/pull/928?src=pr&el=h1) Report  
> Merging [#928](https://codecov.io/gh/boostorg/beast/pull/928?src=pr&el=desc) into [develop](https://codecov.io/gh/boostorg/beast/commit/8414dbf73b50d4b67bd933f2fe20863dd73c3de5?src=pr&el=desc) will **decrease** coverage by `0.04%`.  
> The diff coverage is `97.33%`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/beast/pull/928/graphs/tree.svg?width=650&token=Gq6MFA9JRF&height=150&src=pr)](https://codecov.io/gh/boostorg/beast/pull/928?src=pr&el=tree)  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #928      +/-   ##  
===========================================  
- Coverage    95.71%   95.67%   -0.05%       
===========================================  
  Files          104      104                
  Lines        10453    10424      -29       
===========================================  
- Hits         10005     9973      -32       
- Misses         448      451       +3  
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/beast/pull/928?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [include/boost/beast/http/fields.hpp](https://codecov.io/gh/boostorg/beast/pull/928/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9odHRwL2ZpZWxkcy5ocHA=) | `100% <ø> (ø)` | :arrow_up: |  
| [include/boost/beast/http/impl/fields.ipp](https://codecov.io/gh/boostorg/beast/pull/928/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9odHRwL2ltcGwvZmllbGRzLmlwcA==) | `97.72% <ø> (ø)` | :arrow_up: |  
| [include/boost/beast/websocket/stream.hpp](https://codecov.io/gh/boostorg/beast/pull/928/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC93ZWJzb2NrZXQvc3RyZWFtLmhwcA==) | `100% <ø> (ø)` | :arrow_up: |  
| [include/boost/beast/websocket/impl/teardown.ipp](https://codecov.io/gh/boostorg/beast/pull/928/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC93ZWJzb2NrZXQvaW1wbC90ZWFyZG93bi5pcHA=) | `100% <ø> (ø)` | :arrow_up: |  
| [include/boost/beast/websocket/impl/write.ipp](https://codecov.io/gh/boostorg/beast/pull/928/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC93ZWJzb2NrZXQvaW1wbC93cml0ZS5pcHA=) | `99.75% <100%> (-0.01%)` | :arrow_down: |  
| [include/boost/beast/core/detail/bind\_handler.hpp](https://codecov.io/gh/boostorg/beast/pull/928/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9jb3JlL2RldGFpbC9iaW5kX2hhbmRsZXIuaHBw) | `100% <100%> (ø)` | :arrow_up: |  
| [include/boost/beast/websocket/impl/ping.ipp](https://codecov.io/gh/boostorg/beast/pull/928/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC93ZWJzb2NrZXQvaW1wbC9waW5nLmlwcA==) | `100% <100%> (ø)` | :arrow_up: |  
| [include/boost/beast/websocket/impl/handshake.ipp](https://codecov.io/gh/boostorg/beast/pull/928/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC93ZWJzb2NrZXQvaW1wbC9oYW5kc2hha2UuaXBw) | `100% <100%> (ø)` | :arrow_up: |  
| [include/boost/beast/http/impl/write.ipp](https://codecov.io/gh/boostorg/beast/pull/928/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9odHRwL2ltcGwvd3JpdGUuaXBw) | `93.29% <100%> (-0.14%)` | :arrow_down: |  
| [include/boost/beast/websocket/impl/read.ipp](https://codecov.io/gh/boostorg/beast/pull/928/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC93ZWJzb2NrZXQvaW1wbC9yZWFkLmlwcA==) | `99.67% <100%> (-0.01%)` | :arrow_down: |  
| ... and [12 more](https://codecov.io/gh/boostorg/beast/pull/928/diff?src=pr&el=tree-more) | |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/beast/pull/928?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/beast/pull/928?src=pr&el=footer). Last update [8414dbf...3edb30b](https://codecov.io/gh/boostorg/beast/pull/928?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---
