# #126 Use jump table and better code generation without local_const_stream [Closed]

> Username: sdkrystian  
> Created at: 2020-07-06 18:13:53 UTC  
> Updated at: 2020-08-08 01:54:45 UTC  
> Closed at: 2020-07-20 19:27:45 UTC  
> Url: https://github.com/boostorg/json/pull/126  



---

## Comment 1

> Username: vinniefalco  
> Created_at: 2020-07-06 18:29:40 UTC  
> Updated_at: 2020-07-06 18:34:46 UTC  
> Url: https://github.com/boostorg/json/pull/126#issuecomment-654396961  

The commit message "Remove local streams from parse null, true, and false" adds no information beyond what is already obvious from the diff. Consider "Better code generation without local_const_stream" as an alternative.

---

## Comment 2

> Username: codecov[bot]  
> Created_at: 2020-07-16 03:48:20 UTC  
> Updated_at: 2020-07-16 05:02:15 UTC  
> Url: https://github.com/boostorg/json/pull/126#issuecomment-659140926  

# [Codecov](https://codecov.io/gh/CPPAlliance/json/pull/126?src=pr&el=h1) Report  
> Merging [#126](https://codecov.io/gh/CPPAlliance/json/pull/126?src=pr&el=desc) into [develop](https://codecov.io/gh/CPPAlliance/json/commit/d59bc5d9b7ea2d996af9c3da6b74cef398a37da6&el=desc) will **decrease** coverage by `0.00%`.  
> The diff coverage is `100.00%`.  
  
[![Impacted file tree graph](https://codecov.io/gh/CPPAlliance/json/pull/126/graphs/tree.svg?width=650&height=150&src=pr&token=HNiMmIjyKi)](https://codecov.io/gh/CPPAlliance/json/pull/126?src=pr&el=tree)  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #126      +/-   ##  
===========================================  
- Coverage    99.21%   99.21%   -0.01%       
===========================================  
  Files           60       60                
  Lines         5222     5201      -21       
===========================================  
- Hits          5181     5160      -21       
  Misses          41       41                
```  
  
  
| [Impacted Files](https://codecov.io/gh/CPPAlliance/json/pull/126?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [include/boost/json/detail/basic\_parser.hpp](https://codecov.io/gh/CPPAlliance/json/pull/126/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9qc29uL2RldGFpbC9iYXNpY19wYXJzZXIuaHBw) | `100.00% <ø> (ø)` | |  
| [include/boost/json/basic\_parser.hpp](https://codecov.io/gh/CPPAlliance/json/pull/126/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9qc29uL2Jhc2ljX3BhcnNlci5ocHA=) | `99.19% <100.00%> (-0.02%)` | :arrow_down: |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/CPPAlliance/json/pull/126?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/CPPAlliance/json/pull/126?src=pr&el=footer). Last update [d59bc5d...b7e171a](https://codecov.io/gh/CPPAlliance/json/pull/126?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---

## Comment 3

> Username: vinniefalco  
> Created_at: 2020-07-20 19:27:45 UTC  
> Url: https://github.com/boostorg/json/pull/126#issuecomment-661287983  

Already in #116

---
