# #1322 Fix test::stream async ops for custom result types [Closed]

> Username: djarek  
> Created at: 2018-11-25 18:40:34 UTC  
> Updated at: 2019-09-10 21:02:18 UTC  
> Closed at: 2018-11-27 16:35:03 UTC  
> Url: https://github.com/boostorg/beast/pull/1322  

Async initiating functions must return init.result.get(). Returning the result of post caused a compilation failure for custom completion tokens.

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2018-11-25 19:16:23 UTC  
> Updated_at: 2018-11-25 21:31:17 UTC  
> Url: https://github.com/boostorg/beast/pull/1322#issuecomment-441464714  

# [Codecov](https://codecov.io/gh/boostorg/beast/pull/1322?src=pr&el=h1) Report  
> Merging [#1322](https://codecov.io/gh/boostorg/beast/pull/1322?src=pr&el=desc) into [develop](https://codecov.io/gh/boostorg/beast/commit/4a2fd11fa9c4442d3d102e3a82018197b25eed4c?src=pr&el=desc) will **decrease** coverage by `<.01%`.  
> The diff coverage is `96.77%`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/beast/pull/1322/graphs/tree.svg?width=650&token=Gq6MFA9JRF&height=150&src=pr)](https://codecov.io/gh/boostorg/beast/pull/1322?src=pr&el=tree)  
  
```diff  
@@             Coverage Diff             @@  
##           develop    #1322      +/-   ##  
===========================================  
- Coverage    94.48%   94.47%   -0.01%       
===========================================  
  Files          124      124                
  Lines        11469    11464       -5       
===========================================  
- Hits         10836    10831       -5       
  Misses         633      633  
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/beast/pull/1322?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [include/boost/beast/websocket/impl/teardown.ipp](https://codecov.io/gh/boostorg/beast/pull/1322/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC93ZWJzb2NrZXQvaW1wbC90ZWFyZG93bi5pcHA=) | `100% <ø> (ø)` | :arrow_up: |  
| [include/boost/beast/websocket/impl/write.ipp](https://codecov.io/gh/boostorg/beast/pull/1322/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC93ZWJzb2NrZXQvaW1wbC93cml0ZS5pcHA=) | `97.98% <ø> (ø)` | :arrow_up: |  
| [include/boost/beast/websocket/impl/read.ipp](https://codecov.io/gh/boostorg/beast/pull/1322/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC93ZWJzb2NrZXQvaW1wbC9yZWFkLmlwcA==) | `97.09% <100%> (ø)` | :arrow_up: |  
| [include/boost/beast/websocket/impl/accept.ipp](https://codecov.io/gh/boostorg/beast/pull/1322/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC93ZWJzb2NrZXQvaW1wbC9hY2NlcHQuaXBw) | `100% <100%> (ø)` | :arrow_up: |  
| [include/boost/beast/websocket/impl/ping.ipp](https://codecov.io/gh/boostorg/beast/pull/1322/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC93ZWJzb2NrZXQvaW1wbC9waW5nLmlwcA==) | `100% <100%> (ø)` | :arrow_up: |  
| [include/boost/beast/websocket/impl/close.ipp](https://codecov.io/gh/boostorg/beast/pull/1322/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC93ZWJzb2NrZXQvaW1wbC9jbG9zZS5pcHA=) | `100% <100%> (ø)` | :arrow_up: |  
| [...ude/boost/beast/core/impl/buffered\_read\_stream.ipp](https://codecov.io/gh/boostorg/beast/pull/1322/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9jb3JlL2ltcGwvYnVmZmVyZWRfcmVhZF9zdHJlYW0uaXBw) | `93.22% <100%> (ø)` | :arrow_up: |  
| [include/boost/beast/http/impl/write.ipp](https://codecov.io/gh/boostorg/beast/pull/1322/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9odHRwL2ltcGwvd3JpdGUuaXBw) | `93.69% <100%> (ø)` | :arrow_up: |  
| [...ude/boost/beast/\_experimental/test/impl/stream.ipp](https://codecov.io/gh/boostorg/beast/pull/1322/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9fZXhwZXJpbWVudGFsL3Rlc3QvaW1wbC9zdHJlYW0uaXBw) | `98.49% <100%> (-0.03%)` | :arrow_down: |  
| [include/boost/beast/http/impl/read.ipp](https://codecov.io/gh/boostorg/beast/pull/1322/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9odHRwL2ltcGwvcmVhZC5pcHA=) | `96.33% <50%> (ø)` | :arrow_up: |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/beast/pull/1322?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/beast/pull/1322?src=pr&el=footer). Last update [4a2fd11...8da8af0](https://codecov.io/gh/boostorg/beast/pull/1322?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---

## Review 2 [Commented]

> Username: vinniefalco  
> Created_at: 2018-11-25 19:46:01 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/1322#pullrequestreview-178094414  

📁 include/boost/beast/_experimental/test/impl/stream.ipp

```diff
 264 |+         boost::asio::post(
 265 |+             in_->ioc.get_executor(),
 266 |+             bind_front_handler(
```

> Username: vinniefalco  
> Created_at: 2018-11-25 19:46:01 UTC  
> Updated_at: 2018-11-25 20:37:02 UTC  
> Url: https://github.com/boostorg/beast/pull/1322#discussion_r236087136  

needs qualification, e.g. `beast::bind_front_handler`

> Username: djarek  
> Created_at: 2018-11-25 20:38:41 UTC  
> Url: https://github.com/boostorg/beast/pull/1322#discussion_r236089142  

Done.


---
