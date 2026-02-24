# #1259 Fix for inverse ibeta with large a,b. [Merged]

> Username: jzmaddock  
> Created at: 2025-04-16 16:57:44 UTC  
> Updated at: 2025-04-17 12:42:23 UTC  
> Merged at: 2025-04-17 12:42:06 UTC  
> Closed at: 2025-04-17 12:42:06 UTC  
> Url: https://github.com/boostorg/math/pull/1259  

In this case Temme's method may fail as the incomplete gamma can't cope, but as the function changes over from 0 to 1 very rapidly, we can just use the saddle point as a reasonable starting location for iteration. See https://github.com/scipy/scipy/issues/21725.

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2025-04-17 11:06:32 UTC  
> Updated_at: 2025-04-17 12:42:23 UTC  
> Url: https://github.com/boostorg/math/pull/1259#issuecomment-2812525586  

## [Codecov](https://app.codecov.io/gh/boostorg/math/pull/1259?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
All modified and coverable lines are covered by tests :white_check_mark:  
> Project coverage is 93.84%. Comparing base [(`a2fbc93`)](https://app.codecov.io/gh/boostorg/math/commit/a2fbc935b91b5d9aa411b2027b633b9e241c9637?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) to head [(`deb4391`)](https://app.codecov.io/gh/boostorg/math/commit/deb439132f256e747a5412b968081935c3dea744?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> Report is 4 commits behind head on develop.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/math/pull/1259/graphs/tree.svg?width=650&height=150&src=pr&token=26IDb1OAa7&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/math/pull/1259?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@           Coverage Diff            @@  
##           develop    #1259   +/-   ##  
========================================  
  Coverage    93.84%   93.84%             
========================================  
  Files          658      658             
  Lines        55295    55299    +4       
========================================  
+ Hits         51891    51895    +4       
  Misses        3404     3404             
```  
  
| [Files with missing lines](https://app.codecov.io/gh/boostorg/math/pull/1259?dropdown=coverage&src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [...st/math/special\_functions/detail/ibeta\_inverse.hpp](https://app.codecov.io/gh/boostorg/math/pull/1259?src=pr&el=tree&filepath=include%2Fboost%2Fmath%2Fspecial_functions%2Fdetail%2Fibeta_inverse.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9tYXRoL3NwZWNpYWxfZnVuY3Rpb25zL2RldGFpbC9pYmV0YV9pbnZlcnNlLmhwcA==) | `93.26% <100.00%> (+0.04%)` | :arrow_up: |  
| [test/test\_ibeta\_inv.hpp](https://app.codecov.io/gh/boostorg/math/pull/1259?src=pr&el=tree&filepath=test%2Ftest_ibeta_inv.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC90ZXN0X2liZXRhX2ludi5ocHA=) | `100.00% <100.00%> (ø)` | |  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/math/pull/1259?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/math/pull/1259?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [a2fbc93...deb4391](https://app.codecov.io/gh/boostorg/math/pull/1259?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>  
<details><summary> :rocket: New features to boost your workflow: </summary>  
  
- :snowflake: [Test Analytics](https://docs.codecov.com/docs/test-analytics): Detect flaky tests, report on failures, and find test suite problems.  
</details>

---
