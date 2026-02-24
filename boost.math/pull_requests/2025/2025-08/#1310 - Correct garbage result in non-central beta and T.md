# #1310 Correct garbage result in non-central beta and T. [Merged]

> Username: jzmaddock  
> Created at: 2025-08-25 11:07:41 UTC  
> Updated at: 2025-08-26 09:34:10 UTC  
> Merged at: 2025-08-26 08:27:02 UTC  
> Closed at: 2025-08-26 08:27:02 UTC  
> Url: https://github.com/boostorg/math/pull/1310  

Fixes github.com/boostorg/math/issues/1308.

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2025-08-25 13:24:59 UTC  
> Updated_at: 2025-08-26 08:27:18 UTC  
> Url: https://github.com/boostorg/math/pull/1310#issuecomment-3220267934  

## [Codecov](https://app.codecov.io/gh/boostorg/math/pull/1310?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
:white_check_mark: All modified and coverable lines are covered by tests.  
:white_check_mark: Project coverage is 95.10%. Comparing base ([`c2284b4`](https://app.codecov.io/gh/boostorg/math/commit/c2284b48871396e2edd501afdbdba99169c2b269?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)) to head ([`dac677c`](https://app.codecov.io/gh/boostorg/math/commit/dac677cb6f334a6d5fb39f15538e0aa6f32ed76b?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)).  
:warning: Report is 4 commits behind head on develop.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/math/pull/1310/graphs/tree.svg?width=650&height=150&src=pr&token=26IDb1OAa7&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/math/pull/1310?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@           Coverage Diff            @@  
##           develop    #1310   +/-   ##  
========================================  
  Coverage    95.10%   95.10%             
========================================  
  Files          796      797    +1       
  Lines        67115    67133   +18       
========================================  
+ Hits         63830    63848   +18       
  Misses        3285     3285             
```  
  
| [Files with missing lines](https://app.codecov.io/gh/boostorg/math/pull/1310?dropdown=coverage&src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [...lude/boost/math/distributions/non\_central\_beta.hpp](https://app.codecov.io/gh/boostorg/math/pull/1310?src=pr&el=tree&filepath=include%2Fboost%2Fmath%2Fdistributions%2Fnon_central_beta.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9tYXRoL2Rpc3RyaWJ1dGlvbnMvbm9uX2NlbnRyYWxfYmV0YS5ocHA=) | `92.15% <100.00%> (+0.06%)` | :arrow_up: |  
| [include/boost/math/distributions/non\_central\_t.hpp](https://app.codecov.io/gh/boostorg/math/pull/1310?src=pr&el=tree&filepath=include%2Fboost%2Fmath%2Fdistributions%2Fnon_central_t.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9tYXRoL2Rpc3RyaWJ1dGlvbnMvbm9uX2NlbnRyYWxfdC5ocHA=) | `96.58% <100.00%> (+0.02%)` | :arrow_up: |  
| [test/git\_issue\_1308.cpp](https://app.codecov.io/gh/boostorg/math/pull/1310?src=pr&el=tree&filepath=test%2Fgit_issue_1308.cpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC9naXRfaXNzdWVfMTMwOC5jcHA=) | `100.00% <100.00%> (ø)` | |  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/math/pull/1310?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/math/pull/1310?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [c2284b4...dac677c](https://app.codecov.io/gh/boostorg/math/pull/1310?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
</details>  
<details><summary> :rocket: New features to boost your workflow: </summary>  
  
- :snowflake: [Test Analytics](https://docs.codecov.com/docs/test-analytics): Detect flaky tests, report on failures, and find test suite problems.  
</details>

---
