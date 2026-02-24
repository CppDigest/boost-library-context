# #919 Support for noncopyable completion handlers (HTTP Read/Write) [Closed]

> Username: djarek  
> Created at: 2017-12-02 13:12:31 UTC  
> Updated at: 2019-09-10 21:02:35 UTC  
> Closed at: 2017-12-03 03:51:29 UTC  
> Url: https://github.com/boostorg/beast/pull/919  

HTTP async_read_* and async_write_* will now accept CompletionTokens which produce move-only CompletionHandlers.

---

## Review 1 [Commented]

> Username: vinniefalco  
> Created_at: 2017-12-02 14:03:25 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/919#pullrequestreview-80675767  

📁 test/extras/include/boost/beast/test/stream.hpp

```diff
 704 | 
 705 |-         lambda(state& s, Buffers const& b, Handler&& h)
 705 |+         template <typename DeducedHandler>
```

> Username: vinniefalco  
> Created_at: 2017-12-02 14:03:21 UTC  
> Updated_at: 2017-12-02 21:07:04 UTC  
> Url: https://github.com/boostorg/beast/pull/919#discussion_r154497100  

Style: `template<class DeducedHandler>`


---

## Comment 2

> Username: codecov[bot]  
> Created_at: 2017-12-03 02:01:11 UTC  
> Url: https://github.com/boostorg/beast/pull/919#issuecomment-348734467  

# [Codecov](https://codecov.io/gh/boostorg/beast/pull/919?src=pr&el=h1) Report  
> Merging [#919](https://codecov.io/gh/boostorg/beast/pull/919?src=pr&el=desc) into [develop](https://codecov.io/gh/boostorg/beast/commit/bfd4378c133b2eb35277be8b635adb3f1fdaf09d?src=pr&el=desc) will **increase** coverage by `0.03%`.  
> The diff coverage is `97.97%`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/beast/pull/919/graphs/tree.svg?token=Gq6MFA9JRF&src=pr&width=650&height=150)](https://codecov.io/gh/boostorg/beast/pull/919?src=pr&el=tree)  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #919      +/-   ##  
===========================================  
+ Coverage    95.67%   95.71%   +0.03%       
===========================================  
  Files          104      104                
  Lines        10454    10432      -22       
===========================================  
- Hits         10002     9985      -17       
+ Misses         452      447       -5  
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/beast/pull/919?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [include/boost/beast/http/fields.hpp](https://codecov.io/gh/boostorg/beast/pull/919/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9odHRwL2ZpZWxkcy5ocHA=) | `100% <ø> (ø)` | :arrow_up: |  
| [include/boost/beast/http/impl/fields.ipp](https://codecov.io/gh/boostorg/beast/pull/919/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9odHRwL2ltcGwvZmllbGRzLmlwcA==) | `97.72% <ø> (ø)` | :arrow_up: |  
| [include/boost/beast/websocket/impl/teardown.ipp](https://codecov.io/gh/boostorg/beast/pull/919/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC93ZWJzb2NrZXQvaW1wbC90ZWFyZG93bi5pcHA=) | `100% <ø> (ø)` | :arrow_up: |  
| [include/boost/beast/websocket/impl/handshake.ipp](https://codecov.io/gh/boostorg/beast/pull/919/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC93ZWJzb2NrZXQvaW1wbC9oYW5kc2hha2UuaXBw) | `100% <100%> (ø)` | :arrow_up: |  
| [include/boost/beast/core/detail/bind\_handler.hpp](https://codecov.io/gh/boostorg/beast/pull/919/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9jb3JlL2RldGFpbC9iaW5kX2hhbmRsZXIuaHBw) | `100% <100%> (ø)` | :arrow_up: |  
| [include/boost/beast/websocket/impl/ping.ipp](https://codecov.io/gh/boostorg/beast/pull/919/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC93ZWJzb2NrZXQvaW1wbC9waW5nLmlwcA==) | `100% <100%> (ø)` | :arrow_up: |  
| [include/boost/beast/websocket/impl/close.ipp](https://codecov.io/gh/boostorg/beast/pull/919/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC93ZWJzb2NrZXQvaW1wbC9jbG9zZS5pcHA=) | `99.5% <100%> (+0.48%)` | :arrow_up: |  
| [include/boost/beast/zlib/impl/error.ipp](https://codecov.io/gh/boostorg/beast/pull/919/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC96bGliL2ltcGwvZXJyb3IuaXBw) | `100% <100%> (ø)` | :arrow_up: |  
| [include/boost/beast/websocket/impl/accept.ipp](https://codecov.io/gh/boostorg/beast/pull/919/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC93ZWJzb2NrZXQvaW1wbC9hY2NlcHQuaXBw) | `100% <100%> (ø)` | :arrow_up: |  
| [include/boost/beast/http/impl/read.ipp](https://codecov.io/gh/boostorg/beast/pull/919/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9odHRwL2ltcGwvcmVhZC5pcHA=) | `97% <100%> (-0.05%)` | :arrow_down: |  
| ... and [12 more](https://codecov.io/gh/boostorg/beast/pull/919/diff?src=pr&el=tree-more) | |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/beast/pull/919?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/beast/pull/919?src=pr&el=footer). Last update [bfd4378...6dca449](https://codecov.io/gh/boostorg/beast/pull/919?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---

## Comment 3

> Username: vinniefalco  
> Created_at: 2017-12-03 03:51:29 UTC  
> Url: https://github.com/boostorg/beast/pull/919#issuecomment-348738332  

This is part of #920 now

---
