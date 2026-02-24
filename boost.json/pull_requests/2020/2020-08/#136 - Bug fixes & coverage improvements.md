# #136 Bug fixes & coverage improvements [Closed]

> Username: sdkrystian  
> Created at: 2020-08-08 01:51:28 UTC  
> Updated at: 2020-08-22 18:03:19 UTC  
> Closed at: 2020-08-09 23:16:49 UTC  
> Url: https://github.com/boostorg/json/pull/136  



---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2020-08-08 18:12:33 UTC  
> Updated_at: 2020-08-08 19:32:17 UTC  
> Url: https://github.com/boostorg/json/pull/136#issuecomment-670957820  

# [Codecov](https://codecov.io/gh/CPPAlliance/json/pull/136?src=pr&el=h1) Report  
> Merging [#136](https://codecov.io/gh/CPPAlliance/json/pull/136?src=pr&el=desc) into [develop](https://codecov.io/gh/CPPAlliance/json/commit/95b499a2f547d1c92950bb8b30d95bdd9ae2a0d5&el=desc) will **increase** coverage by `0.55%`.  
> The diff coverage is `100.00%`.  
  
[![Impacted file tree graph](https://codecov.io/gh/CPPAlliance/json/pull/136/graphs/tree.svg?width=650&height=150&src=pr&token=HNiMmIjyKi)](https://codecov.io/gh/CPPAlliance/json/pull/136?src=pr&el=tree)  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #136      +/-   ##  
===========================================  
+ Coverage    97.78%   98.34%   +0.55%       
===========================================  
  Files           62       62                
  Lines         5745     5750       +5       
===========================================  
+ Hits          5618     5655      +37       
+ Misses         127       95      -32       
```  
  
  
| [Impacted Files](https://codecov.io/gh/CPPAlliance/json/pull/136?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [include/boost/json/detail/basic\_parser.hpp](https://codecov.io/gh/CPPAlliance/json/pull/136/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9qc29uL2RldGFpbC9iYXNpY19wYXJzZXIuaHBw) | `100.00% <ø> (ø)` | |  
| [include/boost/json/parse\_options.hpp](https://codecov.io/gh/CPPAlliance/json/pull/136/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9qc29uL3BhcnNlX29wdGlvbnMuaHBw) | `100.00% <ø> (ø)` | |  
| [include/boost/json/basic\_parser.hpp](https://codecov.io/gh/CPPAlliance/json/pull/136/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9qc29uL2Jhc2ljX3BhcnNlci5ocHA=) | `100.00% <100.00%> (+1.55%)` | :arrow_up: |  
| [include/boost/json/detail/stream.hpp](https://codecov.io/gh/CPPAlliance/json/pull/136/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9qc29uL2RldGFpbC9zdHJlYW0uaHBw) | `100.00% <100.00%> (ø)` | |  
| [include/boost/json/impl/parser.ipp](https://codecov.io/gh/CPPAlliance/json/pull/136/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9qc29uL2ltcGwvcGFyc2VyLmlwcA==) | `100.00% <0.00%> (+2.00%)` | :arrow_up: |  
| [include/boost/json/parser.hpp](https://codecov.io/gh/CPPAlliance/json/pull/136/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9qc29uL3BhcnNlci5ocHA=) | `100.00% <0.00%> (+100.00%)` | :arrow_up: |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/CPPAlliance/json/pull/136?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/CPPAlliance/json/pull/136?src=pr&el=footer). Last update [95b499a...bc3d5ad](https://codecov.io/gh/CPPAlliance/json/pull/136?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---

## Comment 2

> Username: vinniefalco  
> Created_at: 2020-08-09 16:00:06 UTC  
> Url: https://github.com/boostorg/json/pull/136#issuecomment-671069180  

I would like to see all the constructors for `parse_options` removed, and the tests fixed.

---

## Comment 3

> Username: vinniefalco  
> Created_at: 2020-08-09 23:17:00 UTC  
> Url: https://github.com/boostorg/json/pull/136#issuecomment-671112774  

Merged most of it (not the `parse_options` bit), thanks.

---
