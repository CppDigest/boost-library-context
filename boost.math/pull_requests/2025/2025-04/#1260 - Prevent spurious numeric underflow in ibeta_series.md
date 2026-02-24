# #1260 Prevent spurious numeric underflow in ibeta_series. [Merged]

> Username: jzmaddock  
> Created at: 2025-04-17 18:51:49 UTC  
> Updated at: 2025-04-18 10:27:00 UTC  
> Merged at: 2025-04-18 10:26:51 UTC  
> Closed at: 2025-04-18 10:26:51 UTC  
> Url: https://github.com/boostorg/math/pull/1260  

Fixes #1247.

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2025-04-17 20:42:00 UTC  
> Updated_at: 2025-04-18 10:26:59 UTC  
> Url: https://github.com/boostorg/math/pull/1260#issuecomment-2813980628  

## [Codecov](https://app.codecov.io/gh/boostorg/math/pull/1260?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
All modified and coverable lines are covered by tests :white_check_mark:  
> Project coverage is 93.84%. Comparing base [(`1290be6`)](https://app.codecov.io/gh/boostorg/math/commit/1290be687d6ee3e850e2d4fdba1b5f301d876575?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) to head [(`f0ef1fd`)](https://app.codecov.io/gh/boostorg/math/commit/f0ef1fd39ebb90689182129b689144ab71e8948c?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> Report is 8 commits behind head on develop.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/math/pull/1260/graphs/tree.svg?width=650&height=150&src=pr&token=26IDb1OAa7&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/math/pull/1260?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@           Coverage Diff            @@  
##           develop    #1260   +/-   ##  
========================================  
  Coverage    93.84%   93.84%             
========================================  
  Files          658      659    +1       
  Lines        55299    55311   +12       
========================================  
+ Hits         51895    51907   +12       
  Misses        3404     3404             
```  
  
| [Files with missing lines](https://app.codecov.io/gh/boostorg/math/pull/1260?dropdown=coverage&src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/math/special\_functions/beta.hpp](https://app.codecov.io/gh/boostorg/math/pull/1260?src=pr&el=tree&filepath=include%2Fboost%2Fmath%2Fspecial_functions%2Fbeta.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9tYXRoL3NwZWNpYWxfZnVuY3Rpb25zL2JldGEuaHBw) | `100.00% <100.00%> (ø)` | |  
| [test/git\_issue\_1247.cpp](https://app.codecov.io/gh/boostorg/math/pull/1260?src=pr&el=tree&filepath=test%2Fgit_issue_1247.cpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC9naXRfaXNzdWVfMTI0Ny5jcHA=) | `100.00% <100.00%> (ø)` | |  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/math/pull/1260?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/math/pull/1260?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [1290be6...f0ef1fd](https://app.codecov.io/gh/boostorg/math/pull/1260?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>  
<details><summary> :rocket: New features to boost your workflow: </summary>  
  
- :snowflake: [Test Analytics](https://docs.codecov.com/docs/test-analytics): Detect flaky tests, report on failures, and find test suite problems.  
</details>

---
