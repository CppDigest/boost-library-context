# #848 caller provided serializer storage [Merged]

> Username: grisumbras  
> Created at: 2023-01-27 17:59:15 UTC  
> Updated at: 2023-01-28 11:04:17 UTC  
> Merged at: 2023-01-28 11:04:16 UTC  
> Closed at: 2023-01-28 11:04:16 UTC  
> Url: https://github.com/boostorg/json/pull/848  

Fix #844

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2023-01-27 18:18:07 UTC  
> Updated_at: 2023-01-27 18:29:15 UTC  
> Url: https://github.com/boostorg/json/pull/848#issuecomment-1406910747  

# [Codecov](https://codecov.io/gh/boostorg/json/pull/848?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#848](https://codecov.io/gh/boostorg/json/pull/848?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (c06b17a) into [develop](https://codecov.io/gh/boostorg/json/commit/d7b7bef7057f3193f43d5390d707a3384c43a8a2?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (d7b7bef) will **increase** coverage by `0.19%`.  
> The diff coverage is `100.00%`.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/json/pull/848/graphs/tree.svg?width=650&height=150&src=pr&token=HNiMmIjyKi&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://codecov.io/gh/boostorg/json/pull/848?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #848      +/-   ##  
===========================================  
+ Coverage    99.00%   99.19%   +0.19%       
===========================================  
  Files           70       70                
  Lines         6837     6849      +12       
===========================================  
+ Hits          6769     6794      +25       
+ Misses          68       55      -13       
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/json/pull/848?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/json/serializer.hpp](https://codecov.io/gh/boostorg/json/pull/848?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9qc29uL3NlcmlhbGl6ZXIuaHBw) | `100.00% <ø> (ø)` | |  
| [include/boost/json/detail/impl/stack.ipp](https://codecov.io/gh/boostorg/json/pull/848?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9qc29uL2RldGFpbC9pbXBsL3N0YWNrLmlwcA==) | `100.00% <100.00%> (ø)` | |  
| [include/boost/json/detail/stack.hpp](https://codecov.io/gh/boostorg/json/pull/848?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9qc29uL2RldGFpbC9zdGFjay5ocHA=) | `100.00% <100.00%> (ø)` | |  
| [include/boost/json/impl/serialize.ipp](https://codecov.io/gh/boostorg/json/pull/848?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9qc29uL2ltcGwvc2VyaWFsaXplLmlwcA==) | `94.93% <100.00%> (+7.59%)` | :arrow_up: |  
| [include/boost/json/impl/serializer.ipp](https://codecov.io/gh/boostorg/json/pull/848?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9qc29uL2ltcGwvc2VyaWFsaXplci5pcHA=) | `98.62% <100.00%> (+1.64%)` | :arrow_up: |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/json/pull/848?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/json/pull/848?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [d7b7bef...c06b17a](https://codecov.io/gh/boostorg/json/pull/848?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---

## Comment 2

> Username: cppalliance-bot  
> Created_at: 2023-01-27 20:27:15 UTC  
> Url: https://github.com/boostorg/json/pull/848#issuecomment-1407040834  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/848/pullrequest-condensed-1241211.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/848/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/848/pullrequest.html)

---
