# #1283 Add macOS long double to fp_traits for double precision [Merged]

> Username: mborland  
> Created at: 2025-06-30 18:01:11 UTC  
> Updated at: 2025-08-29 11:54:43 UTC  
> Merged at: 2025-08-29 11:54:38 UTC  
> Closed at: 2025-08-29 11:54:38 UTC  
> Url: https://github.com/boostorg/math/pull/1283  

Closes: #1282  
  
Changes the traits to show that macOS long double on ARM64 is an alias for double. Removes what seems to be an improper fall through case because this allowed a definition of `<long double, double_precision>` and `<long double, extended_precsion>` of 128-bits of this platform which is the source of the linked issue error.

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2025-06-30 18:57:36 UTC  
> Updated_at: 2025-06-30 19:42:27 UTC  
> Url: https://github.com/boostorg/math/pull/1283#issuecomment-3020360958  

## [Codecov](https://app.codecov.io/gh/boostorg/math/pull/1283?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
All modified and coverable lines are covered by tests :white_check_mark:  
> Project coverage is 95.07%. Comparing base [(`f0a1c2e`)](https://app.codecov.io/gh/boostorg/math/commit/f0a1c2ea7000dd7b3676a9bad7b1350f45b1b74c?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) to head [(`83d7b1a`)](https://app.codecov.io/gh/boostorg/math/commit/83d7b1a50da0cced9deec4f7279ff3f0f5c9b930?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/math/pull/1283/graphs/tree.svg?width=650&height=150&src=pr&token=26IDb1OAa7&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/math/pull/1283?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@           Coverage Diff            @@  
##           develop    #1283   +/-   ##  
========================================  
  Coverage    95.07%   95.07%             
========================================  
  Files          792      792             
  Lines        66882    66882             
========================================  
  Hits         63589    63589             
  Misses        3293     3293             
```  
  
| [Files with missing lines](https://app.codecov.io/gh/boostorg/math/pull/1283?dropdown=coverage&src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [.../boost/math/special\_functions/detail/fp\_traits.hpp](https://app.codecov.io/gh/boostorg/math/pull/1283?src=pr&el=tree&filepath=include%2Fboost%2Fmath%2Fspecial_functions%2Fdetail%2Ffp_traits.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9tYXRoL3NwZWNpYWxfZnVuY3Rpb25zL2RldGFpbC9mcF90cmFpdHMuaHBw) | `100.00% <ø> (ø)` | |  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/math/pull/1283?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/math/pull/1283?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [f0a1c2e...83d7b1a](https://app.codecov.io/gh/boostorg/math/pull/1283?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>  
<details><summary> :rocket: New features to boost your workflow: </summary>  
  
- :snowflake: [Test Analytics](https://docs.codecov.com/docs/test-analytics): Detect flaky tests, report on failures, and find test suite problems.  
</details>

---
