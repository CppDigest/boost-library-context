# #175 feature/optimized_try_emplace [Merged]

> Username: joaquintides  
> Created at: 2022-12-12 08:54:56 UTC  
> Updated at: 2024-08-17 16:49:16 UTC  
> Merged at: 2022-12-13 08:24:57 UTC  
> Closed at: 2022-12-13 08:24:57 UTC  
> Url: https://github.com/boostorg/unordered/pull/175  

* Optimizes `try_emplace` by avoiding `forward_as_tuple` till the last moment (element construction).  
* Extends `try_emplace` so that it can be internally used by `boost::unordered_flat_set`  for heterogeneous insert as part of the ongoing implementation of P2363.

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2022-12-12 11:30:42 UTC  
> Updated_at: 2022-12-12 11:48:31 UTC  
> Url: https://github.com/boostorg/unordered/pull/175#issuecomment-1346318959  

# [Codecov](https://codecov.io/gh/boostorg/unordered/pull/175?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#175](https://codecov.io/gh/boostorg/unordered/pull/175?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (5eda445) into [develop](https://codecov.io/gh/boostorg/unordered/commit/63f07daa88fb5dbd198f79e06facd0079b6c3183?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (63f07da) will **increase** coverage by `0.00%`.  
> The diff coverage is `100.00%`.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/unordered/pull/175/graphs/tree.svg?width=650&height=150&src=pr&token=ZqRPZlJZ5N&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://codecov.io/gh/boostorg/unordered/pull/175?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@           Coverage Diff            @@  
##           develop     #175   +/-   ##  
========================================  
  Coverage    97.68%   97.68%             
========================================  
  Files           83       83             
  Lines        12066    12073    +7       
========================================  
+ Hits         11787    11794    +7       
  Misses         279      279             
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/unordered/pull/175?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/unordered/detail/foa.hpp](https://codecov.io/gh/boostorg/unordered/pull/175/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91bm9yZGVyZWQvZGV0YWlsL2ZvYS5ocHA=) | `98.62% <100.00%> (+0.01%)` | :arrow_up: |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/unordered/pull/175?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/unordered/pull/175?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [63f07da...5eda445](https://codecov.io/gh/boostorg/unordered/pull/175?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---
