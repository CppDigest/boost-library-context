# #902 fix asan failures [Merged]

> Username: grisumbras  
> Created at: 2023-06-02 13:21:25 UTC  
> Updated at: 2023-06-08 13:22:12 UTC  
> Merged at: 2023-06-08 13:22:05 UTC  
> Closed at: 2023-06-08 13:22:05 UTC  
> Url: https://github.com/boostorg/json/pull/902  

Changes to array sizes in tests are due to asan bug.

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2023-06-02 13:41:10 UTC  
> Updated_at: 2023-06-07 12:09:35 UTC  
> Url: https://github.com/boostorg/json/pull/902#issuecomment-1573755135  

## [Codecov](https://app.codecov.io/gh/boostorg/json/pull/902?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#902](https://app.codecov.io/gh/boostorg/json/pull/902?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (3956bdb) into [develop](https://app.codecov.io/gh/boostorg/json/commit/be759c5051b828f77b59d5c67b9b53d7330e627e?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (be759c5) will **increase** coverage by `0.00%`.  
> The diff coverage is `100.00%`.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/json/pull/902/graphs/tree.svg?width=650&height=150&src=pr&token=HNiMmIjyKi&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/json/pull/902?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@           Coverage Diff            @@  
##           develop     #902   +/-   ##  
========================================  
  Coverage    99.25%   99.25%             
========================================  
  Files           72       72             
  Lines         6959     6968    +9       
========================================  
+ Hits          6907     6916    +9       
  Misses          52       52             
```  
  
  
| [Impacted Files](https://app.codecov.io/gh/boostorg/json/pull/902?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/json/basic\_parser\_impl.hpp](https://app.codecov.io/gh/boostorg/json/pull/902?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9qc29uL2Jhc2ljX3BhcnNlcl9pbXBsLmhwcA==) | `98.58% <100.00%> (+<0.01%)` | :arrow_up: |  
| [include/boost/json/impl/value\_stack.ipp](https://app.codecov.io/gh/boostorg/json/pull/902?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9qc29uL2ltcGwvdmFsdWVfc3RhY2suaXBw) | `100.00% <100.00%> (ø)` | |  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/json/pull/902?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/json/pull/902?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [be759c5...3956bdb](https://app.codecov.io/gh/boostorg/json/pull/902?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---

## Comment 2

> Username: cppalliance-bot  
> Created_at: 2023-06-02 14:35:39 UTC  
> Url: https://github.com/boostorg/json/pull/902#issuecomment-1573842698  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/902/pullrequest-condensed-aac0cd3.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/902/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/902/pullrequest.html)

---

## Comment 3

> Username: cppalliance-bot  
> Created_at: 2023-06-07 12:30:48 UTC  
> Url: https://github.com/boostorg/json/pull/902#issuecomment-1580719904  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/902/pullrequest-condensed-0dfb2f2.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/902/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/902/pullrequest.html)

---
