# #500 Use Daniel Lemire's algorithm for parsing doubles [Closed]

> Username: grisumbras  
> Created at: 2021-02-14 15:30:26 UTC  
> Updated at: 2021-03-18 05:28:39 UTC  
> Closed at: 2021-03-17 13:51:50 UTC  
> Url: https://github.com/boostorg/json/pull/500  



---

## Comment 1

> Username: dirtyharrycallahan  
> Created_at: 2021-02-14 15:42:41 UTC  
> Url: https://github.com/boostorg/json/pull/500#issuecomment-778795421  

Eisel-Lemire

---

## Comment 2

> Username: codecov[bot]  
> Created_at: 2021-02-14 16:33:59 UTC  
> Updated_at: 2021-02-14 16:36:07 UTC  
> Url: https://github.com/boostorg/json/pull/500#issuecomment-778802462  

# [Codecov](https://codecov.io/gh/boostorg/json/pull/500?src=pr&el=h1) Report  
> Merging [#500](https://codecov.io/gh/boostorg/json/pull/500?src=pr&el=desc) (23c2b80) into [develop](https://codecov.io/gh/boostorg/json/commit/e6422636b4aaca4db8d19be6585e4ef65be6f9e8?el=desc) (e642263) will **decrease** coverage by `0.10%`.  
> The diff coverage is `92.39%`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/json/pull/500/graphs/tree.svg?width=650&height=150&src=pr&token=HNiMmIjyKi)](https://codecov.io/gh/boostorg/json/pull/500?src=pr&el=tree)  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #500      +/-   ##  
===========================================  
- Coverage    99.10%   99.00%   -0.11%       
===========================================  
  Files           67       68       +1       
  Lines         6047     6116      +69       
===========================================  
+ Hits          5993     6055      +62       
- Misses          54       61       +7       
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/json/pull/500?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [include/boost/json/basic\_parser.hpp](https://codecov.io/gh/boostorg/json/pull/500/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9qc29uL2Jhc2ljX3BhcnNlci5ocHA=) | `100.00% <ø> (ø)` | |  
| [include/boost/json/detail/fast\_double\_parser.h](https://codecov.io/gh/boostorg/json/pull/500/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9qc29uL2RldGFpbC9mYXN0X2RvdWJsZV9wYXJzZXIuaA==) | `89.70% <89.70%> (ø)` | |  
| [include/boost/json/basic\_parser\_impl.hpp](https://codecov.io/gh/boostorg/json/pull/500/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9qc29uL2Jhc2ljX3BhcnNlcl9pbXBsLmhwcA==) | `99.18% <100.00%> (+<0.01%)` | :arrow_up: |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/json/pull/500?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/json/pull/500?src=pr&el=footer). Last update [e642263...23c2b80](https://codecov.io/gh/boostorg/json/pull/500?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---

## Comment 3

> Username: cppalliance-bot  
> Created_at: 2021-02-14 17:28:50 UTC  
> Url: https://github.com/boostorg/json/pull/500#issuecomment-778810091  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/500/pullrequest-condensed-61b6b9e.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/500/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/500/pullrequest.html)

---
