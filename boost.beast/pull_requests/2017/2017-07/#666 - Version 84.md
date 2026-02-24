# #666 Version 84 [Merged]

> Username: vinniefalco  
> Created at: 2017-07-20 19:20:28 UTC  
> Updated at: 2017-07-20 20:20:32 UTC  
> Merged at: 2017-07-20 20:19:43 UTC  
> Closed at: 2017-07-20 20:19:43 UTC  
> Url: https://github.com/boostorg/beast/pull/666  

* Tidy up buffer_front  
* static_buffer::consume improvement  
* multi_buffer is type-check friendly  
* bind_handler allows placeholders  
* Add consuming_buffers::get  
  
WebSocket:  
  
* WebSocket read optimization  
  
API Changes:  
  
* websocket::stream::read_buffer_size is removed  
  
Actions Required:  
  
* Remove calls websocket::stream::read_buffer_size  
* Use read_some and write_some instead of read_frame and write_frame

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2017-07-20 19:20:32 UTC  
> Url: https://github.com/boostorg/beast/pull/666#issuecomment-316803924  

# [Codecov](https://codecov.io/gh/vinniefalco/Beast/pull/666?src=pr&el=h1) Report  
> Merging [#666](https://codecov.io/gh/vinniefalco/Beast/pull/666?src=pr&el=desc) into [develop](https://codecov.io/gh/vinniefalco/Beast/commit/093f44663835e14a811318223a27c96fa2865583?src=pr&el=desc) will **decrease** coverage by `0.69%`.  
> The diff coverage is `81.31%`.  
  
[![Impacted file tree graph](https://codecov.io/gh/vinniefalco/Beast/pull/666/graphs/tree.svg?token=Gq6MFA9JRF&src=pr&height=150&width=650)](https://codecov.io/gh/vinniefalco/Beast/pull/666?src=pr&el=tree)  
  
```diff  
@@            Coverage Diff             @@  
##           develop     #666     +/-   ##  
==========================================  
- Coverage    94.03%   93.34%   -0.7%       
==========================================  
  Files          108      109      +1       
  Lines         8136     8336    +200       
==========================================  
+ Hits          7651     7781    +130       
- Misses         485      555     +70  
```  
  
  
| [Impacted Files](https://codecov.io/gh/vinniefalco/Beast/pull/666?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [include/beast/core/bind\_handler.hpp](https://codecov.io/gh/vinniefalco/Beast/pull/666?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9jb3JlL2JpbmRfaGFuZGxlci5ocHA=) | `100% <ø> (ø)` | :arrow_up: |  
| [include/beast/core/consuming\_buffers.hpp](https://codecov.io/gh/vinniefalco/Beast/pull/666?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9jb3JlL2NvbnN1bWluZ19idWZmZXJzLmhwcA==) | `100% <ø> (ø)` | :arrow_up: |  
| [include/beast/websocket/detail/utf8\_checker.hpp](https://codecov.io/gh/vinniefalco/Beast/pull/666?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC93ZWJzb2NrZXQvZGV0YWlsL3V0ZjhfY2hlY2tlci5ocHA=) | `95.32% <ø> (ø)` | :arrow_up: |  
| [include/beast/websocket/impl/accept.ipp](https://codecov.io/gh/vinniefalco/Beast/pull/666?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC93ZWJzb2NrZXQvaW1wbC9hY2NlcHQuaXBw) | `100% <ø> (ø)` | :arrow_up: |  
| [include/beast/core/buffer\_prefix.hpp](https://codecov.io/gh/vinniefalco/Beast/pull/666?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9jb3JlL2J1ZmZlcl9wcmVmaXguaHBw) | `92.3% <0%> (-7.7%)` | :arrow_down: |  
| [include/beast/websocket/detail/pausation.hpp](https://codecov.io/gh/vinniefalco/Beast/pull/666?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC93ZWJzb2NrZXQvZGV0YWlsL3BhdXNhdGlvbi5ocHA=) | `36.36% <0%> (-39.83%)` | :arrow_down: |  
| [include/beast/websocket/detail/frame.hpp](https://codecov.io/gh/vinniefalco/Beast/pull/666?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC93ZWJzb2NrZXQvZGV0YWlsL2ZyYW1lLmhwcA==) | `95.34% <100%> (ø)` | :arrow_up: |  
| [include/beast/websocket/impl/error.ipp](https://codecov.io/gh/vinniefalco/Beast/pull/666?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC93ZWJzb2NrZXQvaW1wbC9lcnJvci5pcHA=) | `95.65% <100%> (+0.19%)` | :arrow_up: |  
| [include/beast/websocket/impl/close.ipp](https://codecov.io/gh/vinniefalco/Beast/pull/666?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC93ZWJzb2NrZXQvaW1wbC9jbG9zZS5pcHA=) | `100% <100%> (+2.81%)` | :arrow_up: |  
| [include/beast/core/detail/clamp.hpp](https://codecov.io/gh/vinniefalco/Beast/pull/666?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9jb3JlL2RldGFpbC9jbGFtcC5ocHA=) | `100% <100%> (ø)` | :arrow_up: |  
| ... and [19 more](https://codecov.io/gh/vinniefalco/Beast/pull/666?src=pr&el=tree-more) | |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/vinniefalco/Beast/pull/666?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/vinniefalco/Beast/pull/666?src=pr&el=footer). Last update [093f446...32902fa](https://codecov.io/gh/vinniefalco/Beast/pull/666?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---

## Comment 2

> Username: codecov[bot]  
> Created_at: 2017-07-20 19:20:32 UTC  
> Url: https://github.com/boostorg/beast/pull/666#issuecomment-316803926  

# [Codecov](https://codecov.io/gh/vinniefalco/Beast/pull/666?src=pr&el=h1) Report  
> Merging [#666](https://codecov.io/gh/vinniefalco/Beast/pull/666?src=pr&el=desc) into [develop](https://codecov.io/gh/vinniefalco/Beast/commit/093f44663835e14a811318223a27c96fa2865583?src=pr&el=desc) will **decrease** coverage by `0.69%`.  
> The diff coverage is `81.31%`.  
  
[![Impacted file tree graph](https://codecov.io/gh/vinniefalco/Beast/pull/666/graphs/tree.svg?src=pr&token=Gq6MFA9JRF&width=650&height=150)](https://codecov.io/gh/vinniefalco/Beast/pull/666?src=pr&el=tree)  
  
```diff  
@@            Coverage Diff             @@  
##           develop     #666     +/-   ##  
==========================================  
- Coverage    94.03%   93.34%   -0.7%       
==========================================  
  Files          108      109      +1       
  Lines         8136     8336    +200       
==========================================  
+ Hits          7651     7781    +130       
- Misses         485      555     +70  
```  
  
  
| [Impacted Files](https://codecov.io/gh/vinniefalco/Beast/pull/666?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [include/beast/core/bind\_handler.hpp](https://codecov.io/gh/vinniefalco/Beast/pull/666?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9jb3JlL2JpbmRfaGFuZGxlci5ocHA=) | `100% <ø> (ø)` | :arrow_up: |  
| [include/beast/websocket/impl/accept.ipp](https://codecov.io/gh/vinniefalco/Beast/pull/666?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC93ZWJzb2NrZXQvaW1wbC9hY2NlcHQuaXBw) | `100% <ø> (ø)` | :arrow_up: |  
| [include/beast/core/consuming\_buffers.hpp](https://codecov.io/gh/vinniefalco/Beast/pull/666?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9jb3JlL2NvbnN1bWluZ19idWZmZXJzLmhwcA==) | `100% <ø> (ø)` | :arrow_up: |  
| [include/beast/websocket/detail/utf8\_checker.hpp](https://codecov.io/gh/vinniefalco/Beast/pull/666?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC93ZWJzb2NrZXQvZGV0YWlsL3V0ZjhfY2hlY2tlci5ocHA=) | `95.32% <ø> (ø)` | :arrow_up: |  
| [include/beast/core/buffer\_prefix.hpp](https://codecov.io/gh/vinniefalco/Beast/pull/666?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9jb3JlL2J1ZmZlcl9wcmVmaXguaHBw) | `92.3% <0%> (-7.7%)` | :arrow_down: |  
| [include/beast/websocket/detail/pausation.hpp](https://codecov.io/gh/vinniefalco/Beast/pull/666?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC93ZWJzb2NrZXQvZGV0YWlsL3BhdXNhdGlvbi5ocHA=) | `36.36% <0%> (-39.83%)` | :arrow_down: |  
| [include/beast/core/multi\_buffer.hpp](https://codecov.io/gh/vinniefalco/Beast/pull/666?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9jb3JlL211bHRpX2J1ZmZlci5ocHA=) | `100% <100%> (ø)` | :arrow_up: |  
| [include/beast/core/detail/bind\_handler.hpp](https://codecov.io/gh/vinniefalco/Beast/pull/666?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9jb3JlL2RldGFpbC9iaW5kX2hhbmRsZXIuaHBw) | `100% <100%> (ø)` | :arrow_up: |  
| [include/beast/websocket/impl/ping.ipp](https://codecov.io/gh/vinniefalco/Beast/pull/666?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC93ZWJzb2NrZXQvaW1wbC9waW5nLmlwcA==) | `96.34% <100%> (+0.04%)` | :arrow_up: |  
| [include/beast/websocket/impl/close.ipp](https://codecov.io/gh/vinniefalco/Beast/pull/666?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC93ZWJzb2NrZXQvaW1wbC9jbG9zZS5pcHA=) | `100% <100%> (+2.81%)` | :arrow_up: |  
| ... and [19 more](https://codecov.io/gh/vinniefalco/Beast/pull/666?src=pr&el=tree-more) | |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/vinniefalco/Beast/pull/666?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/vinniefalco/Beast/pull/666?src=pr&el=footer). Last update [093f446...32902fa](https://codecov.io/gh/vinniefalco/Beast/pull/666?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---
