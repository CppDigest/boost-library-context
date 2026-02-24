# #138 erase(iterator) perf [Merged]

> Username: cmazakas  
> Created at: 2022-07-26 15:28:06 UTC  
> Updated at: 2022-07-27 14:25:18 UTC  
> Merged at: 2022-07-27 11:41:07 UTC  
> Closed at: 2022-07-27 11:41:07 UTC  
> Url: https://github.com/boostorg/unordered/pull/138  

Add a dedicated `erase_node()` function to the table class used internally by the implementation, creating optimizer-friendly code for cl.exe.  
  
Partially addresses https://github.com/boostorg/unordered/issues/137

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2022-07-26 17:06:38 UTC  
> Updated_at: 2022-07-26 20:59:44 UTC  
> Url: https://github.com/boostorg/unordered/pull/138#issuecomment-1195748837  

# [Codecov](https://codecov.io/gh/boostorg/unordered/pull/138?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#138](https://codecov.io/gh/boostorg/unordered/pull/138?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (a31e894) into [develop](https://codecov.io/gh/boostorg/unordered/commit/3abe5de533a45628755b6ebf518d3a5c37a20377?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (3abe5de) will **decrease** coverage by `0.00%`.  
> The diff coverage is `100.00%`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/unordered/pull/138/graphs/tree.svg?width=650&height=150&src=pr&token=ZqRPZlJZ5N&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://codecov.io/gh/boostorg/unordered/pull/138?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #138      +/-   ##  
===========================================  
- Coverage    97.28%   97.28%   -0.01%       
===========================================  
  Files           75       75                
  Lines         9943     9941       -2       
===========================================  
- Hits          9673     9671       -2       
  Misses         270      270                
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/unordered/pull/138?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/unordered/detail/implementation.hpp](https://codecov.io/gh/boostorg/unordered/pull/138/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91bm9yZGVyZWQvZGV0YWlsL2ltcGxlbWVudGF0aW9uLmhwcA==) | `98.57% <100.00%> (+0.01%)` | :arrow_up: |  
| [include/boost/unordered/unordered\_map.hpp](https://codecov.io/gh/boostorg/unordered/pull/138/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91bm9yZGVyZWQvdW5vcmRlcmVkX21hcC5ocHA=) | `99.84% <100.00%> (-0.01%)` | :arrow_down: |  
| [include/boost/unordered/unordered\_set.hpp](https://codecov.io/gh/boostorg/unordered/pull/138/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91bm9yZGVyZWQvdW5vcmRlcmVkX3NldC5ocHA=) | `98.90% <100.00%> (-0.01%)` | :arrow_down: |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/unordered/pull/138?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/unordered/pull/138?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [3abe5de...a31e894](https://codecov.io/gh/boostorg/unordered/pull/138?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).

---
