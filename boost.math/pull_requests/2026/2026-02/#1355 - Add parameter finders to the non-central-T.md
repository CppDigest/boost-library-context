# #1355 Add parameter finders to the non-central-T. [Merged]

> Username: jzmaddock  
> Created at: 2026-02-01 13:07:59 UTC  
> Updated at: 2026-02-19 17:36:49 UTC  
> Merged at: 2026-02-19 17:35:55 UTC  
> Closed at: 2026-02-19 17:35:55 UTC  
> Url: https://github.com/boostorg/math/pull/1355  

Re-enable old code that had been disabled and fix up the tests and the bugs. See also github.com/boostorg/math/issues/1305.

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2026-02-01 14:25:53 UTC  
> Updated_at: 2026-02-19 17:36:49 UTC  
> Url: https://github.com/boostorg/math/pull/1355#issuecomment-3831125930  

## [Codecov](https://app.codecov.io/gh/boostorg/math/pull/1355?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
:white_check_mark: All modified and coverable lines are covered by tests.  
:white_check_mark: Project coverage is 95.34%. Comparing base ([`bf511b4`](https://app.codecov.io/gh/boostorg/math/commit/bf511b46a69d9d0648fe21a415eb41bdd3e17f78?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)) to head ([`585d317`](https://app.codecov.io/gh/boostorg/math/commit/585d317947455f06aa951e5d8f8fc626d2fc6b39?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)).  
:warning: Report is 53 commits behind head on develop.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/math/pull/1355/graphs/tree.svg?width=650&height=150&src=pr&token=26IDb1OAa7&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/math/pull/1355?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@             Coverage Diff             @@  
##           develop    #1355      +/-   ##  
===========================================  
+ Coverage    95.29%   95.34%   +0.04%       
===========================================  
  Files          814      825      +11       
  Lines        67467    68160     +693       
===========================================  
+ Hits         64294    64987     +693       
  Misses        3173     3173                
```  
  
| [Files with missing lines](https://app.codecov.io/gh/boostorg/math/pull/1355?dropdown=coverage&src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/math/distributions/non\_central\_t.hpp](https://app.codecov.io/gh/boostorg/math/pull/1355?src=pr&el=tree&filepath=include%2Fboost%2Fmath%2Fdistributions%2Fnon_central_t.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9tYXRoL2Rpc3RyaWJ1dGlvbnMvbm9uX2NlbnRyYWxfdC5ocHA=) | `97.20% <100.00%> (+0.63%)` | :arrow_up: |  
| [test/test\_nc\_t.hpp](https://app.codecov.io/gh/boostorg/math/pull/1355?src=pr&el=tree&filepath=test%2Ftest_nc_t.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC90ZXN0X25jX3QuaHBw) | `98.63% <100.00%> (-0.63%)` | :arrow_down: |  
  
... and [19 files with indirect coverage changes](https://app.codecov.io/gh/boostorg/math/pull/1355/indirect-changes?src=pr&el=tree-more&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/math/pull/1355?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/math/pull/1355?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [bf511b4...585d317](https://app.codecov.io/gh/boostorg/math/pull/1355?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
</details>  
<details><summary> :rocket: New features to boost your workflow: </summary>  
  
- :snowflake: [Test Analytics](https://docs.codecov.com/docs/test-analytics): Detect flaky tests, report on failures, and find test suite problems.  
</details>

---
