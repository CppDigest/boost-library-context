# #175 Refactor string parsing [Closed]

> Username: sdkrystian  
> Created at: 2020-08-19 21:03:43 UTC  
> Updated at: 2020-08-22 17:59:17 UTC  
> Closed at: 2020-08-20 16:33:14 UTC  
> Url: https://github.com/boostorg/json/pull/175  



---

## Comment 1

> Username: vinniefalco  
> Created_at: 2020-08-19 23:28:38 UTC  
> Url: https://github.com/boostorg/json/pull/175#issuecomment-676809009  

This has merge conflicts with develop

---

## Comment 2

> Username: sdkrystian  
> Created_at: 2020-08-20 01:55:48 UTC  
> Url: https://github.com/boostorg/json/pull/175#issuecomment-676850123  

@vinniefalco Resolved

---

## Comment 3

> Username: vinniefalco  
> Created_at: 2020-08-20 02:22:15 UTC  
> Url: https://github.com/boostorg/json/pull/175#issuecomment-676856658  

The conflicts are still there after applying the `depth_` decrementing change, see _develop_

---

## Comment 4

> Username: codecov[bot]  
> Created_at: 2020-08-20 06:00:13 UTC  
> Updated_at: 2020-08-20 07:26:17 UTC  
> Url: https://github.com/boostorg/json/pull/175#issuecomment-677228203  

# [Codecov](https://codecov.io/gh/CPPAlliance/json/pull/175?src=pr&el=h1) Report  
> Merging [#175](https://codecov.io/gh/CPPAlliance/json/pull/175?src=pr&el=desc) into [develop](https://codecov.io/gh/CPPAlliance/json/commit/76f2b8d15994b731562685a493829cc22a8464fb&el=desc) will **decrease** coverage by `0.04%`.  
> The diff coverage is `100.00%`.  
  
[![Impacted file tree graph](https://codecov.io/gh/CPPAlliance/json/pull/175/graphs/tree.svg?width=650&height=150&src=pr&token=HNiMmIjyKi)](https://codecov.io/gh/CPPAlliance/json/pull/175?src=pr&el=tree)  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #175      +/-   ##  
===========================================  
- Coverage    98.32%   98.27%   -0.05%       
===========================================  
  Files           64       64                
  Lines         5722     5740      +18       
===========================================  
+ Hits          5626     5641      +15       
- Misses          96       99       +3       
```  
  
  
| [Impacted Files](https://codecov.io/gh/CPPAlliance/json/pull/175?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [include/boost/json/detail/basic\_parser.hpp](https://codecov.io/gh/CPPAlliance/json/pull/175/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9qc29uL2RldGFpbC9iYXNpY19wYXJzZXIuaHBw) | `100.00% <ø> (ø)` | |  
| [include/boost/json/basic\_parser.hpp](https://codecov.io/gh/CPPAlliance/json/pull/175/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9qc29uL2Jhc2ljX3BhcnNlci5ocHA=) | `100.00% <100.00%> (ø)` | |  
| [include/boost/json/detail/sse2.hpp](https://codecov.io/gh/CPPAlliance/json/pull/175/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9qc29uL2RldGFpbC9zc2UyLmhwcA==) | `97.61% <100.00%> (-2.39%)` | :arrow_down: |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/CPPAlliance/json/pull/175?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/CPPAlliance/json/pull/175?src=pr&el=footer). Last update [76f2b8d...7aece71](https://codecov.io/gh/CPPAlliance/json/pull/175?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---

## Comment 5

> Username: sdkrystian  
> Created_at: 2020-08-20 14:39:24 UTC  
> Url: https://github.com/boostorg/json/pull/175#issuecomment-677706393  

@vinniefalco Resolved

---
