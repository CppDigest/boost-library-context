# #315 Fix pretty-printers not working when constexpr variables are optimized away [Merged]

> Username: k3DW  
> Created at: 2025-08-08 03:44:29 UTC  
> Updated at: 2025-08-22 08:16:03 UTC  
> Merged at: 2025-08-22 08:15:49 UTC  
> Closed at: 2025-08-22 08:15:49 UTC  
> Url: https://github.com/boostorg/unordered/pull/315  

Closes #314

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2025-08-08 07:26:06 UTC  
> Updated_at: 2025-08-22 08:15:56 UTC  
> Url: https://github.com/boostorg/unordered/pull/315#issuecomment-3166844778  

## [Codecov](https://app.codecov.io/gh/boostorg/unordered/pull/315?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
:white_check_mark: All modified and coverable lines are covered by tests.  
:white_check_mark: Project coverage is 98.23%. Comparing base ([`59e2a56`](https://app.codecov.io/gh/boostorg/unordered/commit/59e2a562edf0759e0ac52f659d25e0907ed7bec6?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)) to head ([`3f3663c`](https://app.codecov.io/gh/boostorg/unordered/commit/3f3663c38554ff97418c5045922b8366738a5d23?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)).  
:warning: Report is 1 commits behind head on develop.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/unordered/pull/315/graphs/tree.svg?width=650&height=150&src=pr&token=ZqRPZlJZ5N&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/unordered/pull/315?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #315      +/-   ##  
===========================================  
- Coverage    98.24%   98.23%   -0.01%       
===========================================  
  Files          161      161                
  Lines        22224    22224                
===========================================  
- Hits         21833    21832       -1       
- Misses         391      392       +1       
```  
  
| [Files with missing lines](https://app.codecov.io/gh/boostorg/unordered/pull/315?dropdown=coverage&src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/unordered/detail/foa/core.hpp](https://app.codecov.io/gh/boostorg/unordered/pull/315?src=pr&el=tree&filepath=include%2Fboost%2Funordered%2Fdetail%2Ffoa%2Fcore.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91bm9yZGVyZWQvZGV0YWlsL2ZvYS9jb3JlLmhwcA==) | `99.54% <ø> (ø)` | |  
| [include/boost/unordered/detail/implementation.hpp](https://app.codecov.io/gh/boostorg/unordered/pull/315?src=pr&el=tree&filepath=include%2Fboost%2Funordered%2Fdetail%2Fimplementation.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91bm9yZGVyZWQvZGV0YWlsL2ltcGxlbWVudGF0aW9uLmhwcA==) | `99.23% <ø> (ø)` | |  
  
... and [1 file with indirect coverage changes](https://app.codecov.io/gh/boostorg/unordered/pull/315/indirect-changes?src=pr&el=tree-more&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/unordered/pull/315?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/unordered/pull/315?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [59e2a56...3f3663c](https://app.codecov.io/gh/boostorg/unordered/pull/315?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
</details>  
<details><summary> :rocket: New features to boost your workflow: </summary>  
  
- :snowflake: [Test Analytics](https://docs.codecov.com/docs/test-analytics): Detect flaky tests, report on failures, and find test suite problems.  
</details>

---

## Review 2 [Commented]

> Username: joaquintides  
> Created_at: 2025-08-16 08:10:04 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/unordered/pull/315#pullrequestreview-3125742211  

📁 extra/boost_unordered_printers.py

```diff
 194 |+         N = 15 # `group["N"]` may be optimized out
 195 |+         sentinel_ = 1 # `group["sentinel_"]` may be optimized out
 196 |         if self.is_regular_layout(group):
```

> Username: joaquintides  
> Created_at: 2025-08-16 08:10:04 UTC  
> Url: https://github.com/boostorg/unordered/pull/315#discussion_r2280310332  

I'd rather define these as `self.N` and `self.sentinel_` in `BoostUnorderedFoaPrinter.__init__` so that we avoid the duplication of magic constants in `BoostUnorderedFoaPrinter.chidlren`.


---
