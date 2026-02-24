# #922 Move only handler ptr [Closed]

> Username: djarek  
> Created at: 2017-12-04 00:15:00 UTC  
> Updated at: 2019-09-10 21:02:35 UTC  
> Closed at: 2017-12-04 01:51:41 UTC  
> Url: https://github.com/boostorg/beast/pull/922  

It is no longer a reference counted object and now has semantics close to a std::unique_ptr.

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2017-12-04 01:29:06 UTC  
> Url: https://github.com/boostorg/beast/pull/922#issuecomment-348838213  

# [Codecov](https://codecov.io/gh/boostorg/beast/pull/922?src=pr&el=h1) Report  
> Merging [#922](https://codecov.io/gh/boostorg/beast/pull/922?src=pr&el=desc) into [develop](https://codecov.io/gh/boostorg/beast/commit/bfd4378c133b2eb35277be8b635adb3f1fdaf09d?src=pr&el=desc) will **increase** coverage by `<.01%`.  
> The diff coverage is `97.03%`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/beast/pull/922/graphs/tree.svg?height=150&width=650&token=Gq6MFA9JRF&src=pr)](https://codecov.io/gh/boostorg/beast/pull/922?src=pr&el=tree)  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #922      +/-   ##  
===========================================  
+ Coverage    95.67%   95.68%   +<.01%       
===========================================  
  Files          104      104                
  Lines        10454    10402      -52       
===========================================  
- Hits         10002     9953      -49       
+ Misses         452      449       -3  
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/beast/pull/922?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [include/boost/beast/websocket/impl/teardown.ipp](https://codecov.io/gh/boostorg/beast/pull/922/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC93ZWJzb2NrZXQvaW1wbC90ZWFyZG93bi5pcHA=) | `100% <ø> (ø)` | :arrow_up: |  
| [include/boost/beast/http/fields.hpp](https://codecov.io/gh/boostorg/beast/pull/922/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9odHRwL2ZpZWxkcy5ocHA=) | `100% <ø> (ø)` | :arrow_up: |  
| [include/boost/beast/http/impl/fields.ipp](https://codecov.io/gh/boostorg/beast/pull/922/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9odHRwL2ltcGwvZmllbGRzLmlwcA==) | `97.72% <ø> (ø)` | :arrow_up: |  
| [include/boost/beast/websocket/impl/close.ipp](https://codecov.io/gh/boostorg/beast/pull/922/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC93ZWJzb2NrZXQvaW1wbC9jbG9zZS5pcHA=) | `99.5% <100%> (+0.48%)` | :arrow_up: |  
| [include/boost/beast/websocket/impl/error.ipp](https://codecov.io/gh/boostorg/beast/pull/922/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC93ZWJzb2NrZXQvaW1wbC9lcnJvci5pcHA=) | `100% <100%> (ø)` | :arrow_up: |  
| [include/boost/beast/core/handler\_ptr.hpp](https://codecov.io/gh/boostorg/beast/pull/922/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9jb3JlL2hhbmRsZXJfcHRyLmhwcA==) | `100% <100%> (ø)` | :arrow_up: |  
| [include/boost/beast/core/impl/handler\_ptr.ipp](https://codecov.io/gh/boostorg/beast/pull/922/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9jb3JlL2ltcGwvaGFuZGxlcl9wdHIuaXBw) | `100% <100%> (ø)` | :arrow_up: |  
| [include/boost/beast/websocket/impl/handshake.ipp](https://codecov.io/gh/boostorg/beast/pull/922/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC93ZWJzb2NrZXQvaW1wbC9oYW5kc2hha2UuaXBw) | `100% <100%> (ø)` | :arrow_up: |  
| [include/boost/beast/http/impl/read.ipp](https://codecov.io/gh/boostorg/beast/pull/922/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9odHRwL2ltcGwvcmVhZC5pcHA=) | `97% <100%> (-0.05%)` | :arrow_down: |  
| [include/boost/beast/core/detail/bind\_handler.hpp](https://codecov.io/gh/boostorg/beast/pull/922/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9jb3JlL2RldGFpbC9iaW5kX2hhbmRsZXIuaHBw) | `100% <100%> (ø)` | :arrow_up: |  
| ... and [15 more](https://codecov.io/gh/boostorg/beast/pull/922/diff?src=pr&el=tree-more) | |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/beast/pull/922?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/beast/pull/922?src=pr&el=footer). Last update [bfd4378...dc86aa8](https://codecov.io/gh/boostorg/beast/pull/922?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---

## Comment 2

> Username: vinniefalco  
> Created_at: 2017-12-04 01:51:41 UTC  
> Url: https://github.com/boostorg/beast/pull/922#issuecomment-348840561  

Part of #920

---
