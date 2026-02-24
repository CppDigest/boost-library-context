# #104 Disable openCL for appveyor: [Open]

> Username: coder3101  
> Created at: 2020-08-11 06:46:33 UTC  
> Updated at: 2021-09-09 12:45:29 UTC  
> Url: https://github.com/boostorg/ublas/pull/104  

openCL tests on Appveyor are failing due to some vcpkg issue.   
  
As per my mentor @bassoy request, I am removing the openCL tests from the appveyor. The mentioned reason for this removal is:  
  
- We will soon migrate to openMP with new tensor design  
- openCL usage is not documented and hence very likely not being used.

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2020-08-11 06:56:52 UTC  
> Url: https://github.com/boostorg/ublas/pull/104#issuecomment-671765737  

# [Codecov](https://codecov.io/gh/boostorg/ublas/pull/104?src=pr&el=h1) Report  
> Merging [#104](https://codecov.io/gh/boostorg/ublas/pull/104?src=pr&el=desc) into [develop](https://codecov.io/gh/boostorg/ublas/commit/a31e5cffa85f58b64a39fa7c4a1bd3bd9228b069&el=desc) will **not change** coverage.  
> The diff coverage is `n/a`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/ublas/pull/104/graphs/tree.svg?width=650&height=150&src=pr&token=CShoBArRWq)](https://codecov.io/gh/boostorg/ublas/pull/104?src=pr&el=tree)  
  
```diff  
@@           Coverage Diff            @@  
##           develop     #104   +/-   ##  
========================================  
  Coverage    95.33%   95.33%             
========================================  
  Files           22       22             
  Lines         1223     1223             
========================================  
  Hits          1166     1166             
  Misses          57       57             
```  
  
  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/ublas/pull/104?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/ublas/pull/104?src=pr&el=footer). Last update [a31e5cf...e068a23](https://codecov.io/gh/boostorg/ublas/pull/104?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---
