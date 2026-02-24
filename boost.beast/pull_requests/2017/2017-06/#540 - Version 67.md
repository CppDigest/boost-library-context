# #540 Version 67 [Closed]

> Username: vinniefalco  
> Created at: 2017-06-24 23:24:45 UTC  
> Updated at: 2017-06-25 17:18:14 UTC  
> Closed at: 2017-06-25 17:18:14 UTC  
> Url: https://github.com/boostorg/beast/pull/540  

* Fix doc example link  
* Add http-server-small example  
* Merge stream_base to stream and tidy  
* Use boost::string_view  
* Rename to http-server-fast  
  
API Changes:  
  
* control_callback replaces ping_callback  
  
Actions Required:  
  
* Change calls to websocket::stream::ping_callback to use  
  websocket::stream::control_callback  
  
* Change user defined ping callbacks to have the new  
  signature and adjust the callback definition appropriately.  
  
Documentation preview:  
http://vinniefalco.github.io/stage/beast/v67

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2017-06-25 03:41:38 UTC  
> Updated_at: 2017-06-25 06:30:58 UTC  
> Url: https://github.com/boostorg/beast/pull/540#issuecomment-310880284  

# [Codecov](https://codecov.io/gh/vinniefalco/Beast/pull/540?src=pr&el=h1) Report  
> Merging [#540](https://codecov.io/gh/vinniefalco/Beast/pull/540?src=pr&el=desc) into [master](https://codecov.io/gh/vinniefalco/Beast/commit/7fb75d0627f7842a6a706b810d427962509e0e52?src=pr&el=desc) will **decrease** coverage by `0.05%`.  
> The diff coverage is `96.11%`.  
  
[![Impacted file tree graph](https://codecov.io/gh/vinniefalco/Beast/pull/540/graphs/tree.svg?src=pr&token=Gq6MFA9JRF&width=650&height=150)](https://codecov.io/gh/vinniefalco/Beast/pull/540?src=pr&el=tree)  
  
```diff  
@@            Coverage Diff             @@  
##           master     #540      +/-   ##  
==========================================  
- Coverage   93.73%   93.67%   -0.06%       
==========================================  
  Files          96       93       -3       
  Lines        7114     7213      +99       
==========================================  
+ Hits         6668     6757      +89       
- Misses        446      456      +10  
```  
  
  
| [Impacted Files](https://codecov.io/gh/vinniefalco/Beast/pull/540?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [include/beast/core/static\_string.hpp](https://codecov.io/gh/vinniefalco/Beast/pull/540?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9jb3JlL3N0YXRpY19zdHJpbmcuaHBw) | `100% <ø> (ø)` | :arrow_up: |  
| [include/beast/core/string.hpp](https://codecov.io/gh/vinniefalco/Beast/pull/540?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9jb3JlL3N0cmluZy5ocHA=) | `100% <ø> (ø)` | :arrow_up: |  
| [include/beast/core/detail/static\_string.hpp](https://codecov.io/gh/vinniefalco/Beast/pull/540?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9jb3JlL2RldGFpbC9zdGF0aWNfc3RyaW5nLmhwcA==) | `100% <ø> (ø)` | :arrow_up: |  
| [include/beast/websocket/impl/accept.ipp](https://codecov.io/gh/vinniefalco/Beast/pull/540?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC93ZWJzb2NrZXQvaW1wbC9hY2NlcHQuaXBw) | `100% <100%> (ø)` | :arrow_up: |  
| [include/beast/websocket/detail/frame.hpp](https://codecov.io/gh/vinniefalco/Beast/pull/540?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC93ZWJzb2NrZXQvZGV0YWlsL2ZyYW1lLmhwcA==) | `95.23% <100%> (+1.68%)` | :arrow_up: |  
| [include/beast/websocket/impl/write.ipp](https://codecov.io/gh/vinniefalco/Beast/pull/540?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC93ZWJzb2NrZXQvaW1wbC93cml0ZS5pcHA=) | `86.97% <100%> (ø)` | :arrow_up: |  
| [include/beast/websocket/stream.hpp](https://codecov.io/gh/vinniefalco/Beast/pull/540?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC93ZWJzb2NrZXQvc3RyZWFtLmhwcA==) | `91.66% <100%> (+1.19%)` | :arrow_up: |  
| [include/beast/websocket/impl/read.ipp](https://codecov.io/gh/vinniefalco/Beast/pull/540?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC93ZWJzb2NrZXQvaW1wbC9yZWFkLmlwcA==) | `86.31% <75%> (-0.28%)` | :arrow_down: |  
| [include/beast/websocket/impl/stream.ipp](https://codecov.io/gh/vinniefalco/Beast/pull/540?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC93ZWJzb2NrZXQvaW1wbC9zdHJlYW0uaXBw) | `96.33% <97.45%> (+1.16%)` | :arrow_up: |  
| [include/beast/http/empty\_body.hpp](https://codecov.io/gh/vinniefalco/Beast/pull/540?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9odHRwL2VtcHR5X2JvZHkuaHBw) | `57.14% <0%> (-14.29%)` | :arrow_down: |  
| ... and [13 more](https://codecov.io/gh/vinniefalco/Beast/pull/540?src=pr&el=tree-more) | |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/vinniefalco/Beast/pull/540?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/vinniefalco/Beast/pull/540?src=pr&el=footer). Last update [7fb75d0...6a0af1f](https://codecov.io/gh/vinniefalco/Beast/pull/540?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---
