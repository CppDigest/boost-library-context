# #69 trac-1078: given to_iso_string can print special values, make from_iso_string read them [Merged]

> Username: jeking3  
> Created at: 2018-01-07 13:50:20 UTC  
> Updated at: 2018-01-22 20:49:29 UTC  
> Merged at: 2018-01-22 20:49:27 UTC  
> Closed at: 2018-01-22 20:49:27 UTC  
> Url: https://github.com/boostorg/date_time/pull/69  

https://svn.boost.org/trac10/ticket/1078

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2018-01-22 14:34:34 UTC  
> Url: https://github.com/boostorg/date_time/pull/69#issuecomment-359440590  

# [Codecov](https://codecov.io/gh/boostorg/date_time/pull/69?src=pr&el=h1) Report  
> Merging [#69](https://codecov.io/gh/boostorg/date_time/pull/69?src=pr&el=desc) into [develop](https://codecov.io/gh/boostorg/date_time/commit/d8a1ed32fb19f81dd58ee3afd856b82393bb3497?src=pr&el=desc) will **increase** coverage by `0.05%`.  
> The diff coverage is `n/a`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/date_time/pull/69/graphs/tree.svg?height=150&width=650&src=pr&token=nDoh7t8f6g)](https://codecov.io/gh/boostorg/date_time/pull/69?src=pr&el=tree)  
  
```diff  
@@             Coverage Diff             @@  
##           develop      #69      +/-   ##  
===========================================  
+ Coverage    93.71%   93.77%   +0.05%       
===========================================  
  Files           80       80                
  Lines         5015     5031      +16       
===========================================  
+ Hits          4700     4718      +18       
+ Misses         315      313       -2  
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/date_time/pull/69?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [special\_values\_parser.hpp](https://codecov.io/gh/boostorg/date_time/pull/69/diff?src=pr&el=tree#diff-c3BlY2lhbF92YWx1ZXNfcGFyc2VyLmhwcA==) | `100% <0%> (ø)` | :arrow_up: |  
| [time\_parsing.hpp](https://codecov.io/gh/boostorg/date_time/pull/69/diff?src=pr&el=tree#diff-dGltZV9wYXJzaW5nLmhwcA==) | `98.18% <0%> (+0.14%)` | :arrow_up: |  
| [posix\_time/time\_formatters.hpp](https://codecov.io/gh/boostorg/date_time/pull/69/diff?src=pr&el=tree#diff-cG9zaXhfdGltZS90aW1lX2Zvcm1hdHRlcnMuaHBw) | `87.85% <0%> (+1.86%)` | :arrow_up: |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/date_time/pull/69?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/date_time/pull/69?src=pr&el=footer). Last update [d8a1ed3...20cf81c](https://codecov.io/gh/boostorg/date_time/pull/69?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---

## Comment 2

> Username: pdimov  
> Created_at: 2018-01-22 17:26:02 UTC  
> Url: https://github.com/boostorg/date_time/pull/69#issuecomment-359500068  

Requiring roundtrip seems reasonable to me.

---
