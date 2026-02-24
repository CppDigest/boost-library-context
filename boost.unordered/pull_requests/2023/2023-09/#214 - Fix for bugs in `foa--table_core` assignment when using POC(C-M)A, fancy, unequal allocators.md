# #214 Fix for bugs in `foa::table_core` assignment when using POC(C|M)A, fancy, unequal allocators [Merged]

> Username: joaquintides  
> Created at: 2023-09-25 18:48:27 UTC  
> Updated at: 2023-09-26 18:08:18 UTC  
> Merged at: 2023-09-26 18:08:14 UTC  
> Closed at: 2023-09-26 18:08:14 UTC  
> Url: https://github.com/boostorg/unordered/pull/214  

When using propagating, fancy allocators that don't compare equal, `foa::table_core` copy and move assignment deallocated storage with a different allocator than the one used for allocation. The test suite has been updated to explicitly cover these two bugs. The fix includes some slight refactoring of the existing `arrays_holder` machinery as it's now being used in more places.

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2023-09-26 14:20:02 UTC  
> Url: https://github.com/boostorg/unordered/pull/214#issuecomment-1735643728  

## [Codecov](https://app.codecov.io/gh/boostorg/unordered/pull/214?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#214](https://app.codecov.io/gh/boostorg/unordered/pull/214?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (e0598b5) into [develop](https://app.codecov.io/gh/boostorg/unordered/commit/b445ff639de4d6a9bdb5668f08584dfcc0b748ca?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (b445ff6) will **increase** coverage by `0.00%`.  
> The diff coverage is `100.00%`.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/unordered/pull/214/graphs/tree.svg?width=650&height=150&src=pr&token=ZqRPZlJZ5N&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/unordered/pull/214?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@           Coverage Diff            @@  
##           develop     #214   +/-   ##  
========================================  
  Coverage    98.13%   98.14%             
========================================  
  Files          141      141             
  Lines        19508    19518   +10       
========================================  
+ Hits         19145    19156   +11       
+ Misses         363      362    -1       
```  
  
  
| [Files](https://app.codecov.io/gh/boostorg/unordered/pull/214?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [...de/boost/unordered/detail/foa/concurrent\_table.hpp](https://app.codecov.io/gh/boostorg/unordered/pull/214?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91bm9yZGVyZWQvZGV0YWlsL2ZvYS9jb25jdXJyZW50X3RhYmxlLmhwcA==) | `99.66% <100.00%> (-0.01%)` | :arrow_down: |  
| [include/boost/unordered/detail/foa/core.hpp](https://app.codecov.io/gh/boostorg/unordered/pull/214?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91bm9yZGVyZWQvZGV0YWlsL2ZvYS9jb3JlLmhwcA==) | `100.00% <100.00%> (+0.16%)` | :arrow_up: |  
| [include/boost/unordered/detail/foa/table.hpp](https://app.codecov.io/gh/boostorg/unordered/pull/214?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91bm9yZGVyZWQvZGV0YWlsL2ZvYS90YWJsZS5ocHA=) | `100.00% <100.00%> (ø)` | |  
| [test/cfoa/assign\_tests.cpp](https://app.codecov.io/gh/boostorg/unordered/pull/214?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC9jZm9hL2Fzc2lnbl90ZXN0cy5jcHA=) | `100.00% <100.00%> (ø)` | |  
  
... and [1 file with indirect coverage changes](https://app.codecov.io/gh/boostorg/unordered/pull/214/indirect-changes?src=pr&el=tree-more&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/unordered/pull/214?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/unordered/pull/214?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [b445ff6...e0598b5](https://app.codecov.io/gh/boostorg/unordered/pull/214?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---
