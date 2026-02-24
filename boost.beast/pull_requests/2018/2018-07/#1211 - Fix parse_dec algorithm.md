# #1211 Fix parse_dec algorithm [Closed]

> Username: compmaniak  
> Created at: 2018-07-29 23:02:54 UTC  
> Updated at: 2018-07-30 03:20:35 UTC  
> Closed at: 2018-07-30 03:20:35 UTC  
> Url: https://github.com/boostorg/beast/pull/1211  

Current `parse_dec` implementation is wrong in general. Iterating over `[it, last)` range implies that you shouldn't dereference an iterator pointing to `last`. However parsing actually stops when `*last` is not a digit. Also function unable to parse a number close to `numeric_limis<T>::max()` (for example 6553x for `unsigned short` argument).

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2018-07-29 23:15:51 UTC  
> Url: https://github.com/boostorg/beast/pull/1211#issuecomment-408713091  

# [Codecov](https://codecov.io/gh/boostorg/beast/pull/1211?src=pr&el=h1) Report  
> Merging [#1211](https://codecov.io/gh/boostorg/beast/pull/1211?src=pr&el=desc) into [develop](https://codecov.io/gh/boostorg/beast/commit/28f03be5b779a8e768df4b33f7b49c266f0413dc?src=pr&el=desc) will **increase** coverage by `<.01%`.  
> The diff coverage is `100%`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/beast/pull/1211/graphs/tree.svg?height=150&src=pr&token=Gq6MFA9JRF&width=650)](https://codecov.io/gh/boostorg/beast/pull/1211?src=pr&el=tree)  
  
```diff  
@@             Coverage Diff             @@  
##           develop    #1211      +/-   ##  
===========================================  
+ Coverage    95.51%   95.52%   +<.01%       
===========================================  
  Files          113      113                
  Lines        11062    11064       +2       
===========================================  
+ Hits         10566    10569       +3       
+ Misses         496      495       -1  
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/beast/pull/1211?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [include/boost/beast/http/detail/basic\_parser.hpp](https://codecov.io/gh/boostorg/beast/pull/1211/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9odHRwL2RldGFpbC9iYXNpY19wYXJzZXIuaHBw) | `90.11% <100%> (+0.05%)` | :arrow_up: |  
| [include/boost/beast/websocket/impl/close.ipp](https://codecov.io/gh/boostorg/beast/pull/1211/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC93ZWJzb2NrZXQvaW1wbC9jbG9zZS5pcHA=) | `100% <0%> (+0.5%)` | :arrow_up: |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/beast/pull/1211?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/beast/pull/1211?src=pr&el=footer). Last update [28f03be...e673eb2](https://codecov.io/gh/boostorg/beast/pull/1211?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---

## Comment 2

> Username: vinniefalco  
> Created_at: 2018-07-30 00:01:52 UTC  
> Url: https://github.com/boostorg/beast/pull/1211#issuecomment-408715502  

Whoops! Thanks!

---
