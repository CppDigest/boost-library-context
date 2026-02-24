# #1352 Pick up arm-none-eabi float traits [Merged]

> Username: ckormanyos  
> Created at: 2026-01-24 19:30:17 UTC  
> Updated at: 2026-01-24 21:37:20 UTC  
> Merged at: 2026-01-24 21:34:14 UTC  
> Closed at: 2026-01-24 21:34:14 UTC  
> Url: https://github.com/boostorg/math/pull/1352  



---

## Comment 1

> Username: ckormanyos  
> Created_at: 2026-01-24 19:34:36 UTC  
> Url: https://github.com/boostorg/math/pull/1352#issuecomment-3795395020  

See re-openend #1348

---

## Comment 2

> Username: ckormanyos  
> Created_at: 2026-01-24 19:35:44 UTC  
> Url: https://github.com/boostorg/math/pull/1352#issuecomment-3795396477  

I suppose I should eventually add a bare-metal sanity check in CI/CD, as I/we usually do now in new projects.

---

## Comment 3

> Username: ckormanyos  
> Created_at: 2026-01-24 20:09:23 UTC  
> Url: https://github.com/boostorg/math/pull/1352#issuecomment-3795445252  

The failure(s) in the `inspect` job are unrelated, but probably need to be dealt with prior to 1.91.

---

## Comment 4

> Username: codecov[bot]  
> Created_at: 2026-01-24 21:20:50 UTC  
> Updated_at: 2026-01-24 21:37:20 UTC  
> Url: https://github.com/boostorg/math/pull/1352#issuecomment-3795526140  

## [Codecov](https://app.codecov.io/gh/boostorg/math/pull/1352?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
:white_check_mark: All modified and coverable lines are covered by tests.  
:white_check_mark: Project coverage is 95.29%. Comparing base ([`627174f`](https://app.codecov.io/gh/boostorg/math/commit/627174f9160680d424882d9f219d9b3397eee1f8?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)) to head ([`2f7b18c`](https://app.codecov.io/gh/boostorg/math/commit/2f7b18c126914aab9117e391299f9a556121c99e?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)).  
:warning: Report is 3 commits behind head on develop.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/math/pull/1352/graphs/tree.svg?width=650&height=150&src=pr&token=26IDb1OAa7&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/math/pull/1352?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@           Coverage Diff            @@  
##           develop    #1352   +/-   ##  
========================================  
  Coverage    95.29%   95.29%             
========================================  
  Files          814      814             
  Lines        67422    67422             
========================================  
  Hits         64249    64249             
  Misses        3173     3173             
```  
  
| [Files with missing lines](https://app.codecov.io/gh/boostorg/math/pull/1352?dropdown=coverage&src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [.../boost/math/special\_functions/detail/fp\_traits.hpp](https://app.codecov.io/gh/boostorg/math/pull/1352?src=pr&el=tree&filepath=include%2Fboost%2Fmath%2Fspecial_functions%2Fdetail%2Ffp_traits.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9tYXRoL3NwZWNpYWxfZnVuY3Rpb25zL2RldGFpbC9mcF90cmFpdHMuaHBw) | `100.00% <ø> (ø)` | |  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/math/pull/1352?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/math/pull/1352?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [627174f...2f7b18c](https://app.codecov.io/gh/boostorg/math/pull/1352?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
</details>  
<details><summary> :rocket: New features to boost your workflow: </summary>  
  
- :snowflake: [Test Analytics](https://docs.codecov.com/docs/test-analytics): Detect flaky tests, report on failures, and find test suite problems.  
</details>

---
