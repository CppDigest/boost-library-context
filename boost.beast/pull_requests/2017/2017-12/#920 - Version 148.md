# #920 Version 148 [Merged]

> Username: vinniefalco  
> Created at: 2017-12-02 14:59:32 UTC  
> Updated at: 2017-12-09 23:27:33 UTC  
> Merged at: 2017-12-08 17:58:09 UTC  
> Closed at: 2017-12-08 17:58:09 UTC  
> Url: https://github.com/boostorg/beast/pull/920  

This merges **v144-hf1** from master  
  
* Install codecov on codecov CI targets only  
* Update reports for hybrid assessment  
* Handle invalid deflate frames

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2017-12-02 17:14:39 UTC  
> Updated_at: 2017-12-05 09:45:22 UTC  
> Url: https://github.com/boostorg/beast/pull/920#issuecomment-348705781  

# [Codecov](https://codecov.io/gh/boostorg/beast/pull/920?src=pr&el=h1) Report  
> Merging [#920](https://codecov.io/gh/boostorg/beast/pull/920?src=pr&el=desc) into [develop](https://codecov.io/gh/boostorg/beast/commit/bfd4378c133b2eb35277be8b635adb3f1fdaf09d?src=pr&el=desc) will **decrease** coverage by `<.01%`.  
> The diff coverage is `96.32%`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/beast/pull/920/graphs/tree.svg?src=pr&token=Gq6MFA9JRF&height=150&width=650)](https://codecov.io/gh/boostorg/beast/pull/920?src=pr&el=tree)  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #920      +/-   ##  
===========================================  
- Coverage    95.67%   95.67%   -0.01%       
===========================================  
  Files          104      104                
  Lines        10454    10403      -51       
===========================================  
- Hits         10002     9953      -49       
+ Misses         452      450       -2  
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/beast/pull/920?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [include/boost/beast/http/fields.hpp](https://codecov.io/gh/boostorg/beast/pull/920/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9odHRwL2ZpZWxkcy5ocHA=) | `100% <ø> (ø)` | :arrow_up: |  
| [include/boost/beast/http/impl/fields.ipp](https://codecov.io/gh/boostorg/beast/pull/920/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9odHRwL2ltcGwvZmllbGRzLmlwcA==) | `97.72% <ø> (ø)` | :arrow_up: |  
| [include/boost/beast/websocket/impl/teardown.ipp](https://codecov.io/gh/boostorg/beast/pull/920/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC93ZWJzb2NrZXQvaW1wbC90ZWFyZG93bi5pcHA=) | `100% <ø> (ø)` | :arrow_up: |  
| [include/boost/beast/core/detail/bind\_handler.hpp](https://codecov.io/gh/boostorg/beast/pull/920/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9jb3JlL2RldGFpbC9iaW5kX2hhbmRsZXIuaHBw) | `100% <100%> (ø)` | :arrow_up: |  
| [include/boost/beast/core/handler\_ptr.hpp](https://codecov.io/gh/boostorg/beast/pull/920/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9jb3JlL2hhbmRsZXJfcHRyLmhwcA==) | `100% <100%> (ø)` | :arrow_up: |  
| [include/boost/beast/websocket/impl/error.ipp](https://codecov.io/gh/boostorg/beast/pull/920/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC93ZWJzb2NrZXQvaW1wbC9lcnJvci5pcHA=) | `100% <100%> (ø)` | :arrow_up: |  
| [include/boost/beast/websocket/impl/write.ipp](https://codecov.io/gh/boostorg/beast/pull/920/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC93ZWJzb2NrZXQvaW1wbC93cml0ZS5pcHA=) | `99.75% <100%> (-0.01%)` | :arrow_down: |  
| [include/boost/beast/websocket/impl/accept.ipp](https://codecov.io/gh/boostorg/beast/pull/920/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC93ZWJzb2NrZXQvaW1wbC9hY2NlcHQuaXBw) | `100% <100%> (ø)` | :arrow_up: |  
| [include/boost/beast/core/impl/handler\_ptr.ipp](https://codecov.io/gh/boostorg/beast/pull/920/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9jb3JlL2ltcGwvaGFuZGxlcl9wdHIuaXBw) | `100% <100%> (ø)` | :arrow_up: |  
| [include/boost/beast/http/impl/read.ipp](https://codecov.io/gh/boostorg/beast/pull/920/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9odHRwL2ltcGwvcmVhZC5pcHA=) | `97% <100%> (-0.05%)` | :arrow_down: |  
| ... and [15 more](https://codecov.io/gh/boostorg/beast/pull/920/diff?src=pr&el=tree-more) | |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/beast/pull/920?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/beast/pull/920?src=pr&el=footer). Last update [bfd4378...8414dbf](https://codecov.io/gh/boostorg/beast/pull/920?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---
