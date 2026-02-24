# #1970 Remove BOOST_BEAST_NO_DEPRECATED macro (API Change): [Merged]

> Username: madmongo1  
> Created at: 2020-06-01 14:46:26 UTC  
> Updated at: 2020-06-04 13:58:24 UTC  
> Merged at: 2020-06-04 13:58:24 UTC  
> Closed at: 2020-06-04 13:58:24 UTC  
> Url: https://github.com/boostorg/beast/pull/1970  

API Changes:  
  
The macro BOOST_BEAST_NO_DEPRECATED will no longer be noticed by Beast. The only way to  
enable deprecated functionality is now the macro BOOST_BEAST_ALLOW_DEPRECATED which is  
undefined by default. That is, all deprecated behaviour is disabled by default.  
  
refs #1934

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2020-06-02 03:41:44 UTC  
> Updated_at: 2020-06-03 16:19:19 UTC  
> Url: https://github.com/boostorg/beast/pull/1970#issuecomment-637252318  

# [Codecov](https://codecov.io/gh/boostorg/beast/pull/1970?src=pr&el=h1) Report  
> Merging [#1970](https://codecov.io/gh/boostorg/beast/pull/1970?src=pr&el=desc) into [develop](https://codecov.io/gh/boostorg/beast/commit/13a928a872a54f0a90b4a166e0792efeaebf7538&el=desc) will **not change** coverage.  
> The diff coverage is `n/a`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/beast/pull/1970/graphs/tree.svg?width=650&height=150&src=pr&token=Gq6MFA9JRF)](https://codecov.io/gh/boostorg/beast/pull/1970?src=pr&el=tree)  
  
```diff  
@@           Coverage Diff            @@  
##           develop    #1970   +/-   ##  
========================================  
  Coverage    95.13%   95.13%             
========================================  
  Files          155      155             
  Lines        12057    12057             
========================================  
  Hits         11470    11470             
  Misses         587      587             
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/beast/pull/1970?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [include/boost/beast/core/buffers\_adaptor.hpp](https://codecov.io/gh/boostorg/beast/pull/1970/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9jb3JlL2J1ZmZlcnNfYWRhcHRvci5ocHA=) | `100.00% <ø> (ø)` | |  
| [include/boost/beast/core/flat\_buffer.hpp](https://codecov.io/gh/boostorg/beast/pull/1970/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9jb3JlL2ZsYXRfYnVmZmVyLmhwcA==) | `100.00% <ø> (ø)` | |  
| [include/boost/beast/core/flat\_static\_buffer.hpp](https://codecov.io/gh/boostorg/beast/pull/1970/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9jb3JlL2ZsYXRfc3RhdGljX2J1ZmZlci5ocHA=) | `100.00% <ø> (ø)` | |  
| [include/boost/beast/core/multi\_buffer.hpp](https://codecov.io/gh/boostorg/beast/pull/1970/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9jb3JlL211bHRpX2J1ZmZlci5ocHA=) | `100.00% <ø> (ø)` | |  
| [include/boost/beast/core/static\_buffer.hpp](https://codecov.io/gh/boostorg/beast/pull/1970/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9jb3JlL3N0YXRpY19idWZmZXIuaHBw) | `100.00% <ø> (ø)` | |  
| [include/boost/beast/websocket/impl/accept.hpp](https://codecov.io/gh/boostorg/beast/pull/1970/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC93ZWJzb2NrZXQvaW1wbC9hY2NlcHQuaHBw) | `100.00% <ø> (ø)` | |  
| [include/boost/beast/websocket/impl/handshake.hpp](https://codecov.io/gh/boostorg/beast/pull/1970/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC93ZWJzb2NrZXQvaW1wbC9oYW5kc2hha2UuaHBw) | `98.26% <ø> (ø)` | |  
| [include/boost/beast/websocket/stream.hpp](https://codecov.io/gh/boostorg/beast/pull/1970/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC93ZWJzb2NrZXQvc3RyZWFtLmhwcA==) | `100.00% <ø> (ø)` | |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/beast/pull/1970?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/beast/pull/1970?src=pr&el=footer). Last update [13a928a...0fa658f](https://codecov.io/gh/boostorg/beast/pull/1970?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---
