# #250 Refactor test_formatting [Merged]

> Username: Flamefire  
> Created at: 2024-12-25 17:06:28 UTC  
> Updated at: 2024-12-27 11:13:25 UTC  
> Merged at: 2024-12-27 11:13:10 UTC  
> Closed at: 2024-12-27 11:13:10 UTC  
> Url: https://github.com/boostorg/locale/pull/250  

- Make spelling of "ICU" consistently uppercase (except at start of names)  
- Move definitions related to existance of ICU together and sort by name

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2024-12-26 03:04:28 UTC  
> Updated_at: 2024-12-27 11:13:25 UTC  
> Url: https://github.com/boostorg/locale/pull/250#issuecomment-2562105235  

## [Codecov](https://app.codecov.io/gh/boostorg/locale/pull/250?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
All modified and coverable lines are covered by tests :white_check_mark:  
> Project coverage is 95.84%. Comparing base [(`087dfb6`)](https://app.codecov.io/gh/boostorg/locale/commit/087dfb61d619d0f5b3faddd9eb1e06dbec6ad319?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) to head [(`5fc4048`)](https://app.codecov.io/gh/boostorg/locale/commit/5fc4048c92f5974d78b18e5bb969993be5f6c14d?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> Report is 2 commits behind head on develop.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/locale/pull/250/graphs/tree.svg?width=650&height=150&src=pr&token=vF6mWvgGUn&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/locale/pull/250?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@           Coverage Diff            @@  
##           develop     #250   +/-   ##  
========================================  
  Coverage    95.84%   95.84%             
========================================  
  Files          117      117             
  Lines        10410    10410             
========================================  
  Hits          9977     9977             
  Misses         433      433             
```  
  
| [Files with missing lines](https://app.codecov.io/gh/boostorg/locale/pull/250?dropdown=coverage&src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [test/formatting\_common.hpp](https://app.codecov.io/gh/boostorg/locale/pull/250?src=pr&el=tree&filepath=test%2Fformatting_common.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC9mb3JtYXR0aW5nX2NvbW1vbi5ocHA=) | `100.00% <ø> (ø)` | |  
| [test/test\_formatting.cpp](https://app.codecov.io/gh/boostorg/locale/pull/250?src=pr&el=tree&filepath=test%2Ftest_formatting.cpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC90ZXN0X2Zvcm1hdHRpbmcuY3Bw) | `100.00% <100.00%> (ø)` | |  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/locale/pull/250?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/locale/pull/250?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [087dfb6...5fc4048](https://app.codecov.io/gh/boostorg/locale/pull/250?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---
