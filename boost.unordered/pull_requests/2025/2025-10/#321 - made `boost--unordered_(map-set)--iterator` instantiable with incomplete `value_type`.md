# #321 made `boost::unordered_[multi](map|set)::[const_][local_]iterator` instantiable with incomplete `value_type` [Merged]

> Username: joaquintides  
> Created at: 2025-10-11 16:06:19 UTC  
> Updated at: 2025-10-12 11:14:26 UTC  
> Merged at: 2025-10-12 11:14:16 UTC  
> Closed at: 2025-10-12 11:14:16 UTC  
> Url: https://github.com/boostorg/unordered/pull/321  

Addresses #319.

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2025-10-12 10:40:55 UTC  
> Updated_at: 2025-10-12 11:14:26 UTC  
> Url: https://github.com/boostorg/unordered/pull/321#issuecomment-3394146058  

## [Codecov](https://app.codecov.io/gh/boostorg/unordered/pull/321?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
:white_check_mark: All modified and coverable lines are covered by tests.  
:white_check_mark: Project coverage is 98.23%. Comparing base ([`4b76ac8`](https://app.codecov.io/gh/boostorg/unordered/commit/4b76ac84ecd2086da4786c1c49919373570c1ffa?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)) to head ([`eba3e85`](https://app.codecov.io/gh/boostorg/unordered/commit/eba3e857e5db6e6874791a84404163bc227d6b5c?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)).  
:warning: Report is 1 commits behind head on develop.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/unordered/pull/321/graphs/tree.svg?width=650&height=150&src=pr&token=ZqRPZlJZ5N&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/unordered/pull/321?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@           Coverage Diff            @@  
##           develop     #321   +/-   ##  
========================================  
  Coverage    98.23%   98.23%             
========================================  
  Files          161      161             
  Lines        22223    22239   +16       
========================================  
+ Hits         21831    21847   +16       
  Misses         392      392             
```  
  
| [Files with missing lines](https://app.codecov.io/gh/boostorg/unordered/pull/321?dropdown=coverage&src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/unordered/detail/fca.hpp](https://app.codecov.io/gh/boostorg/unordered/pull/321?src=pr&el=tree&filepath=include%2Fboost%2Funordered%2Fdetail%2Ffca.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91bm9yZGVyZWQvZGV0YWlsL2ZjYS5ocHA=) | `99.35% <100.00%> (ø)` | |  
| [include/boost/unordered/detail/implementation.hpp](https://app.codecov.io/gh/boostorg/unordered/pull/321?src=pr&el=tree&filepath=include%2Fboost%2Funordered%2Fdetail%2Fimplementation.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91bm9yZGVyZWQvZGV0YWlsL2ltcGxlbWVudGF0aW9uLmhwcA==) | `99.23% <ø> (ø)` | |  
| [test/unordered/incomplete\_test.cpp](https://app.codecov.io/gh/boostorg/unordered/pull/321?src=pr&el=tree&filepath=test%2Funordered%2Fincomplete_test.cpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC91bm9yZGVyZWQvaW5jb21wbGV0ZV90ZXN0LmNwcA==) | `73.33% <100.00%> (+14.71%)` | :arrow_up: |  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/unordered/pull/321?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/unordered/pull/321?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [4b76ac8...eba3e85](https://app.codecov.io/gh/boostorg/unordered/pull/321?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
</details>  
<details><summary> :rocket: New features to boost your workflow: </summary>  
  
- :snowflake: [Test Analytics](https://docs.codecov.com/docs/test-analytics): Detect flaky tests, report on failures, and find test suite problems.  
</details>

---
