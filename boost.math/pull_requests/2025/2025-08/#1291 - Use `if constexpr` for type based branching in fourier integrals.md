# #1291 Use `if constexpr` for type based branching in fourier integrals [Merged]

> Username: mborland  
> Created at: 2025-08-01 16:41:34 UTC  
> Updated at: 2025-08-01 23:30:14 UTC  
> Merged at: 2025-08-01 18:40:35 UTC  
> Closed at: 2025-08-01 18:40:35 UTC  
> Url: https://github.com/boostorg/math/pull/1291  

See: #1290 comments

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2025-08-01 18:05:00 UTC  
> Updated_at: 2025-08-01 23:30:14 UTC  
> Url: https://github.com/boostorg/math/pull/1291#issuecomment-3145361879  

## [Codecov](https://app.codecov.io/gh/boostorg/math/pull/1291?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
:white_check_mark: All modified and coverable lines are covered by tests.  
:white_check_mark: Project coverage is 95.07%. Comparing base ([`3d86cef`](https://app.codecov.io/gh/boostorg/math/commit/3d86cefe375f153713ded6ca2b1f2293f0589466?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)) to head ([`31b94bf`](https://app.codecov.io/gh/boostorg/math/commit/31b94bfc38031e8bd797938759edb1ae2cedf963?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)).  
:warning: Report is 2 commits behind head on develop.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/math/pull/1291/graphs/tree.svg?width=650&height=150&src=pr&token=26IDb1OAa7&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/math/pull/1291?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@           Coverage Diff            @@  
##           develop    #1291   +/-   ##  
========================================  
  Coverage    95.07%   95.07%             
========================================  
  Files          792      792             
  Lines        66887    66887             
========================================  
  Hits         63596    63596             
  Misses        3291     3291             
```  
  
| [Files with missing lines](https://app.codecov.io/gh/boostorg/math/pull/1291?dropdown=coverage&src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [...adrature/detail/ooura\_fourier\_integrals\_detail.hpp](https://app.codecov.io/gh/boostorg/math/pull/1291?src=pr&el=tree&filepath=include%2Fboost%2Fmath%2Fquadrature%2Fdetail%2Fooura_fourier_integrals_detail.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9tYXRoL3F1YWRyYXR1cmUvZGV0YWlsL29vdXJhX2ZvdXJpZXJfaW50ZWdyYWxzX2RldGFpbC5ocHA=) | `89.50% <ø> (ø)` | |  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/math/pull/1291?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/math/pull/1291?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [3d86cef...31b94bf](https://app.codecov.io/gh/boostorg/math/pull/1291?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
</details>  
<details><summary> :rocket: New features to boost your workflow: </summary>  
  
- :snowflake: [Test Analytics](https://docs.codecov.com/docs/test-analytics): Detect flaky tests, report on failures, and find test suite problems.  
</details>

---
