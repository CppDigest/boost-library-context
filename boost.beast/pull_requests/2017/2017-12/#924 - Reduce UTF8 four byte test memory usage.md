# #924 Reduce UTF8 four byte test memory usage [Closed]

> Username: miguelportilla  
> Created at: 2017-12-05 22:01:49 UTC  
> Updated at: 2017-12-05 22:49:04 UTC  
> Closed at: 2017-12-05 22:49:04 UTC  
> Url: https://github.com/boostorg/beast/pull/924  

This commit may address the memory consumption issue Travis is having with the four-byte UTF8 unit test.

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2017-12-05 22:17:14 UTC  
> Url: https://github.com/boostorg/beast/pull/924#issuecomment-349460267  

# [Codecov](https://codecov.io/gh/boostorg/beast/pull/924?src=pr&el=h1) Report  
> Merging [#924](https://codecov.io/gh/boostorg/beast/pull/924?src=pr&el=desc) into [develop](https://codecov.io/gh/boostorg/beast/commit/bfd4378c133b2eb35277be8b635adb3f1fdaf09d?src=pr&el=desc) will **increase** coverage by `0.03%`.  
> The diff coverage is `83.33%`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/beast/pull/924/graphs/tree.svg?height=150&token=Gq6MFA9JRF&src=pr&width=650)](https://codecov.io/gh/boostorg/beast/pull/924?src=pr&el=tree)  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #924      +/-   ##  
===========================================  
+ Coverage    95.67%   95.71%   +0.03%       
===========================================  
  Files          104      104                
  Lines        10454    10453       -1       
===========================================  
+ Hits         10002    10005       +3       
+ Misses         452      448       -4  
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/beast/pull/924?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [include/boost/beast/zlib/impl/error.ipp](https://codecov.io/gh/boostorg/beast/pull/924/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC96bGliL2ltcGwvZXJyb3IuaXBw) | `100% <100%> (ø)` | :arrow_up: |  
| [include/boost/beast/websocket/impl/error.ipp](https://codecov.io/gh/boostorg/beast/pull/924/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC93ZWJzb2NrZXQvaW1wbC9lcnJvci5pcHA=) | `100% <100%> (ø)` | :arrow_up: |  
| [include/boost/beast/websocket/impl/read.ipp](https://codecov.io/gh/boostorg/beast/pull/924/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC93ZWJzb2NrZXQvaW1wbC9yZWFkLmlwcA==) | `99.67% <80%> (-0.33%)` | :arrow_down: |  
| [include/boost/beast/websocket/impl/close.ipp](https://codecov.io/gh/boostorg/beast/pull/924/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC93ZWJzb2NrZXQvaW1wbC9jbG9zZS5pcHA=) | `99.51% <0%> (+0.48%)` | :arrow_up: |  
| [include/boost/beast/zlib/detail/inflate\_stream.hpp](https://codecov.io/gh/boostorg/beast/pull/924/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC96bGliL2RldGFpbC9pbmZsYXRlX3N0cmVhbS5ocHA=) | `88.39% <0%> (+0.96%)` | :arrow_up: |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/beast/pull/924?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/beast/pull/924?src=pr&el=footer). Last update [bfd4378...5adf3af](https://codecov.io/gh/boostorg/beast/pull/924?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---
