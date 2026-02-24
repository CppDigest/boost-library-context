# #1298 Add new constant `log_pi` [Merged]

> Username: andrjohns  
> Created at: 2025-08-13 13:27:44 UTC  
> Updated at: 2025-08-14 09:15:40 UTC  
> Merged at: 2025-08-14 09:15:40 UTC  
> Closed at: 2025-08-14 09:15:40 UTC  
> Url: https://github.com/boostorg/math/pull/1298  

Adds the definition and tests of a new constant for `log_pi`, used across multiple distribution functions.  
  
Let me know if I've missed anything!

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2025-08-13 14:22:55 UTC  
> Updated_at: 2025-08-13 19:56:59 UTC  
> Url: https://github.com/boostorg/math/pull/1298#issuecomment-3184145008  

## [Codecov](https://app.codecov.io/gh/boostorg/math/pull/1298?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
:white_check_mark: All modified and coverable lines are covered by tests.  
:white_check_mark: Project coverage is 95.08%. Comparing base ([`4d7d6e1`](https://app.codecov.io/gh/boostorg/math/commit/4d7d6e1982ec8c663c95853956789119ff9d230a?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)) to head ([`4b201ab`](https://app.codecov.io/gh/boostorg/math/commit/4b201ab83ba29568284609e0251f26bae6eabd8d?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)).  
  
<details><summary>Additional details and impacted files</summary>  
  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/math/pull/1298/graphs/tree.svg?width=650&height=150&src=pr&token=26IDb1OAa7&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/math/pull/1298?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@             Coverage Diff             @@  
##           develop    #1298      +/-   ##  
===========================================  
+ Coverage    91.51%   95.08%   +3.56%       
===========================================  
  Files          558      792     +234       
  Lines        53881    66920   +13039       
===========================================  
+ Hits         49311    63629   +14318       
+ Misses        4570     3291    -1279       
```  
  
| [Files with missing lines](https://app.codecov.io/gh/boostorg/math/pull/1298?dropdown=coverage&src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [...clude/boost/math/constants/calculate\_constants.hpp](https://app.codecov.io/gh/boostorg/math/pull/1298?src=pr&el=tree&filepath=include%2Fboost%2Fmath%2Fconstants%2Fcalculate_constants.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9tYXRoL2NvbnN0YW50cy9jYWxjdWxhdGVfY29uc3RhbnRzLmhwcA==) | `100.00% <ø> (ø)` | |  
| [include/boost/math/constants/constants.hpp](https://app.codecov.io/gh/boostorg/math/pull/1298?src=pr&el=tree&filepath=include%2Fboost%2Fmath%2Fconstants%2Fconstants.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9tYXRoL2NvbnN0YW50cy9jb25zdGFudHMuaHBw) | `100.00% <100.00%> (ø)` | |  
| [test/test\_constants.cpp](https://app.codecov.io/gh/boostorg/math/pull/1298?src=pr&el=tree&filepath=test%2Ftest_constants.cpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC90ZXN0X2NvbnN0YW50cy5jcHA=) | `99.51% <100.00%> (+<0.01%)` | :arrow_up: |  
  
... and [334 files with indirect coverage changes](https://app.codecov.io/gh/boostorg/math/pull/1298/indirect-changes?src=pr&el=tree-more&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/math/pull/1298?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/math/pull/1298?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [4d7d6e1...4b201ab](https://app.codecov.io/gh/boostorg/math/pull/1298?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
</details>  
<details><summary> :rocket: New features to boost your workflow: </summary>  
  
- :snowflake: [Test Analytics](https://docs.codecov.com/docs/test-analytics): Detect flaky tests, report on failures, and find test suite problems.  
</details>

---

## Comment 2

> Username: ckormanyos  
> Created_at: 2025-08-14 06:05:41 UTC  
> Url: https://github.com/boostorg/math/pull/1298#issuecomment-3187078821  

It looks good to me. Thanks for contributing a nice, useful constant number.  
  
John, Matt should we merge?

---

## Review 3 [Approved]

> Username: mborland  
> Created_at: 2025-08-14 07:54:29 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/math/pull/1298#pullrequestreview-3119430990  

Looks good to me. I pushed a commit adding it to the documentation index of pi related constants. Once it cycles I can merge.

---
