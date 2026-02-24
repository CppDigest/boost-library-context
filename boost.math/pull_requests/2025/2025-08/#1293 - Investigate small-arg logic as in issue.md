# #1293 Investigate small-arg logic as in issue [Merged]

> Username: ckormanyos  
> Created at: 2025-08-07 09:03:40 UTC  
> Updated at: 2025-08-09 15:07:44 UTC  
> Merged at: 2025-08-09 15:07:37 UTC  
> Closed at: 2025-08-09 15:07:37 UTC  
> Url: https://github.com/boostorg/math/pull/1293  



---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2025-08-07 09:58:12 UTC  
> Updated_at: 2025-08-09 15:07:44 UTC  
> Url: https://github.com/boostorg/math/pull/1293#issuecomment-3163382577  

## [Codecov](https://app.codecov.io/gh/boostorg/math/pull/1293?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
:white_check_mark: All modified and coverable lines are covered by tests.  
:white_check_mark: Project coverage is 95.08%. Comparing base ([`c68d746`](https://app.codecov.io/gh/boostorg/math/commit/c68d7463d73732bef065936e607dc45cc11920bb?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)) to head ([`93717ba`](https://app.codecov.io/gh/boostorg/math/commit/93717bab24254d6bcd5356d7b9ea97ee9be52e13?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)).  
:warning: Report is 6 commits behind head on develop.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/math/pull/1293/graphs/tree.svg?width=650&height=150&src=pr&token=26IDb1OAa7&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/math/pull/1293?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@           Coverage Diff            @@  
##           develop    #1293   +/-   ##  
========================================  
  Coverage    95.07%   95.08%             
========================================  
  Files          792      792             
  Lines        66887    66911   +24       
========================================  
+ Hits         63596    63620   +24       
  Misses        3291     3291             
```  
  
| [Files with missing lines](https://app.codecov.io/gh/boostorg/math/pull/1293?dropdown=coverage&src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [.../boost/math/special\_functions/detail/bessel\_jn.hpp](https://app.codecov.io/gh/boostorg/math/pull/1293?src=pr&el=tree&filepath=include%2Fboost%2Fmath%2Fspecial_functions%2Fdetail%2Fbessel_jn.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9tYXRoL3NwZWNpYWxfZnVuY3Rpb25zL2RldGFpbC9iZXNzZWxfam4uaHBw) | `100.00% <100.00%> (ø)` | |  
| [.../boost/math/special\_functions/detail/bessel\_jy.hpp](https://app.codecov.io/gh/boostorg/math/pull/1293?src=pr&el=tree&filepath=include%2Fboost%2Fmath%2Fspecial_functions%2Fdetail%2Fbessel_jy.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9tYXRoL3NwZWNpYWxfZnVuY3Rpb25zL2RldGFpbC9iZXNzZWxfankuaHBw) | `100.00% <100.00%> (ø)` | |  
| [test/test\_bessel\_j.cpp](https://app.codecov.io/gh/boostorg/math/pull/1293?src=pr&el=tree&filepath=test%2Ftest_bessel_j.cpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC90ZXN0X2Jlc3NlbF9qLmNwcA==) | `97.36% <ø> (ø)` | |  
| [test/test\_bessel\_j.hpp](https://app.codecov.io/gh/boostorg/math/pull/1293?src=pr&el=tree&filepath=test%2Ftest_bessel_j.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC90ZXN0X2Jlc3NlbF9qLmhwcA==) | `100.00% <100.00%> (ø)` | |  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/math/pull/1293?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/math/pull/1293?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [c68d746...93717ba](https://app.codecov.io/gh/boostorg/math/pull/1293?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
</details>  
<details><summary> :rocket: New features to boost your workflow: </summary>  
  
- :snowflake: [Test Analytics](https://docs.codecov.com/docs/test-analytics): Detect flaky tests, report on failures, and find test suite problems.  
</details>

---
