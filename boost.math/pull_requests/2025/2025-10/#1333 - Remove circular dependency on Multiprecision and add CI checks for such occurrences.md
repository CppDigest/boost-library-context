# #1333 Remove circular dependency on Multiprecision and add CI checks for such occurrences [Merged]

> Username: mborland  
> Created at: 2025-10-30 10:33:54 UTC  
> Updated at: 2025-10-30 17:32:36 UTC  
> Merged at: 2025-10-30 11:54:09 UTC  
> Closed at: 2025-10-30 11:54:09 UTC  
> Url: https://github.com/boostorg/math/pull/1333  



---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2025-10-30 11:52:21 UTC  
> Updated_at: 2025-10-30 17:32:36 UTC  
> Url: https://github.com/boostorg/math/pull/1333#issuecomment-3467629440  

## [Codecov](https://app.codecov.io/gh/boostorg/math/pull/1333?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
:white_check_mark: All modified and coverable lines are covered by tests.  
:white_check_mark: Project coverage is 95.28%. Comparing base ([`6ee28f2`](https://app.codecov.io/gh/boostorg/math/commit/6ee28f29f6889a543900d562479284790ddc9b08?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)) to head ([`0321de7`](https://app.codecov.io/gh/boostorg/math/commit/0321de76f600330e7f8c79363fc5d6d1c5230af5?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)).  
:warning: Report is 3 commits behind head on develop.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/math/pull/1333/graphs/tree.svg?width=650&height=150&src=pr&token=26IDb1OAa7&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/math/pull/1333?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@             Coverage Diff             @@  
##           develop    #1333      +/-   ##  
===========================================  
+ Coverage    95.24%   95.28%   +0.04%       
===========================================  
  Files          814      814                
  Lines        69309    67364    -1945       
===========================================  
- Hits         66015    64191    -1824       
+ Misses        3294     3173     -121       
```  
  
| [Files with missing lines](https://app.codecov.io/gh/boostorg/math/pull/1333?dropdown=coverage&src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [...ion/detail/reverse\_mode\_autodiff\_erf\_overloads.hpp](https://app.codecov.io/gh/boostorg/math/pull/1333?src=pr&el=tree&filepath=include%2Fboost%2Fmath%2Fdifferentiation%2Fdetail%2Freverse_mode_autodiff_erf_overloads.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9tYXRoL2RpZmZlcmVudGlhdGlvbi9kZXRhaWwvcmV2ZXJzZV9tb2RlX2F1dG9kaWZmX2VyZl9vdmVybG9hZHMuaHBw) | `100.00% <ø> (ø)` | |  
  
... and [495 files with indirect coverage changes](https://app.codecov.io/gh/boostorg/math/pull/1333/indirect-changes?src=pr&el=tree-more&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/math/pull/1333?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/math/pull/1333?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [6ee28f2...0321de7](https://app.codecov.io/gh/boostorg/math/pull/1333?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
</details>  
<details><summary> :rocket: New features to boost your workflow: </summary>  
  
- :snowflake: [Test Analytics](https://docs.codecov.com/docs/test-analytics): Detect flaky tests, report on failures, and find test suite problems.  
</details>

---
