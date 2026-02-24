# #414 Use switch instead of jump table [Closed]

> Username: sdkrystian  
> Created at: 2020-09-25 16:18:44 UTC  
> Updated at: 2020-09-28 16:04:26 UTC  
> Closed at: 2020-09-28 16:04:26 UTC  
> Url: https://github.com/boostorg/json/pull/414  



---

## Comment 1

> Username: cppalliance-bot  
> Created_at: 2020-09-25 17:27:03 UTC  
> Url: https://github.com/boostorg/json/pull/414#issuecomment-699055026  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/414/pullrequest-condensed-b226236.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/414/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/414/pullrequest.html)

---

## Comment 2

> Username: codecov[bot]  
> Created_at: 2020-09-25 18:19:07 UTC  
> Updated_at: 2020-09-25 18:19:10 UTC  
> Url: https://github.com/boostorg/json/pull/414#issuecomment-699080614  

# [Codecov](https://codecov.io/gh/CPPAlliance/json/pull/414?src=pr&el=h1) Report  
> Merging [#414](https://codecov.io/gh/CPPAlliance/json/pull/414?src=pr&el=desc) into [develop](https://codecov.io/gh/CPPAlliance/json/commit/6ddddfb16f9b54407d153abdda11a29f74642854?el=desc) will **increase** coverage by `0.00%`.  
> The diff coverage is `100.00%`.  
  
[![Impacted file tree graph](https://codecov.io/gh/CPPAlliance/json/pull/414/graphs/tree.svg?width=650&height=150&src=pr&token=HNiMmIjyKi)](https://codecov.io/gh/CPPAlliance/json/pull/414?src=pr&el=tree)  
  
```diff  
@@           Coverage Diff            @@  
##           develop     #414   +/-   ##  
========================================  
  Coverage    98.40%   98.41%             
========================================  
  Files           67       68    +1       
  Lines         5833     5863   +30       
========================================  
+ Hits          5740     5770   +30       
  Misses          93       93             
```  
  
  
| [Impacted Files](https://codecov.io/gh/CPPAlliance/json/pull/414?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [include/boost/json/detail/basic\_parser.hpp](https://codecov.io/gh/CPPAlliance/json/pull/414/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9qc29uL2RldGFpbC9iYXNpY19wYXJzZXIuaHBw) | `100.00% <ø> (ø)` | |  
| [include/boost/json/basic\_parser.hpp](https://codecov.io/gh/CPPAlliance/json/pull/414/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9qc29uL2Jhc2ljX3BhcnNlci5ocHA=) | `99.33% <100.00%> (+<0.01%)` | :arrow_up: |  
| [include/boost/json/array.hpp](https://codecov.io/gh/CPPAlliance/json/pull/414/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9qc29uL2FycmF5LmhwcA==) | `100.00% <0.00%> (ø)` | |  
| [include/boost/json/value.hpp](https://codecov.io/gh/CPPAlliance/json/pull/414/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9qc29uL3ZhbHVlLmhwcA==) | `100.00% <0.00%> (ø)` | |  
| [include/boost/json/object.hpp](https://codecov.io/gh/CPPAlliance/json/pull/414/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9qc29uL29iamVjdC5ocHA=) | `100.00% <0.00%> (ø)` | |  
| [include/boost/json/pilfer.hpp](https://codecov.io/gh/CPPAlliance/json/pull/414/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9qc29uL3BpbGZlci5ocHA=) | `100.00% <0.00%> (ø)` | |  
| [include/boost/json/detail/impl/except.ipp](https://codecov.io/gh/CPPAlliance/json/pull/414/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9qc29uL2RldGFpbC9pbXBsL2V4Y2VwdC5pcHA=) | `100.00% <0.00%> (ø)` | |  
| [include/boost/json/impl/kind.ipp](https://codecov.io/gh/CPPAlliance/json/pull/414/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9qc29uL2ltcGwva2luZC5pcHA=) | `100.00% <0.00%> (ø)` | |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/CPPAlliance/json/pull/414?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/CPPAlliance/json/pull/414?src=pr&el=footer). Last update [6ddddfb...3a9fb63](https://codecov.io/gh/CPPAlliance/json/pull/414?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---

## Comment 3

> Username: sdkrystian  
> Created_at: 2020-09-28 16:04:26 UTC  
> Url: https://github.com/boostorg/json/pull/414#issuecomment-700129635  

Addressed by c547ca9d21c55bd045fcc13455c8dd311c7619ce

---
