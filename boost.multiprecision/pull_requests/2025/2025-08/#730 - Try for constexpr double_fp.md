# #730 Try for constexpr double_fp [Merged]

> Username: ckormanyos  
> Created at: 2025-08-31 13:01:09 UTC  
> Updated at: 2025-09-01 23:24:56 UTC  
> Merged at: 2025-09-01 17:27:39 UTC  
> Closed at: 2025-09-01 17:27:39 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/730  



---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2025-09-01 17:25:07 UTC  
> Updated_at: 2025-09-01 23:24:56 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/730#issuecomment-3242973948  

## [Codecov](https://app.codecov.io/gh/boostorg/multiprecision/pull/730?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
:white_check_mark: All modified and coverable lines are covered by tests.  
:white_check_mark: Project coverage is 96.2%. Comparing base ([`fc508e8`](https://app.codecov.io/gh/boostorg/multiprecision/commit/fc508e85876577ec3609608ef8ac621ea4781b27?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)) to head ([`4aa8952`](https://app.codecov.io/gh/boostorg/multiprecision/commit/4aa8952e8d855d91ab8503d174117280be771937?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)).  
:warning: Report is 9 commits behind head on develop.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/multiprecision/pull/730/graphs/tree.svg?width=650&height=150&src=pr&token=SDDBym7Pc9&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/multiprecision/pull/730?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@            Coverage Diff            @@  
##           develop    #730     +/-   ##  
=========================================  
+ Coverage     96.2%   96.2%   +0.1%       
=========================================  
  Files          297     297               
  Lines        31177   31190     +13       
=========================================  
+ Hits         29965   29978     +13       
  Misses        1212    1212               
```  
  
| [Files with missing lines](https://app.codecov.io/gh/boostorg/multiprecision/pull/730?dropdown=coverage&src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [...ecision/cpp\_df\_qf/cpp\_df\_qf\_detail\_ccmath\_fabs.hpp](https://app.codecov.io/gh/boostorg/multiprecision/pull/730?src=pr&el=tree&filepath=include%2Fboost%2Fmultiprecision%2Fcpp_df_qf%2Fcpp_df_qf_detail_ccmath_fabs.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9tdWx0aXByZWNpc2lvbi9jcHBfZGZfcWYvY3BwX2RmX3FmX2RldGFpbF9jY21hdGhfZmFicy5ocHA=) | `100.0% <100.0%> (ø)` | |  
| [...cision/cpp\_df\_qf/cpp\_df\_qf\_detail\_ccmath\_floor.hpp](https://app.codecov.io/gh/boostorg/multiprecision/pull/730?src=pr&el=tree&filepath=include%2Fboost%2Fmultiprecision%2Fcpp_df_qf%2Fcpp_df_qf_detail_ccmath_floor.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9tdWx0aXByZWNpc2lvbi9jcHBfZGZfcWYvY3BwX2RmX3FmX2RldGFpbF9jY21hdGhfZmxvb3IuaHBw) | `100.0% <100.0%> (ø)` | |  
| [...recision/cpp\_df\_qf/cpp\_df\_qf\_detail\_ccmath\_fma.hpp](https://app.codecov.io/gh/boostorg/multiprecision/pull/730?src=pr&el=tree&filepath=include%2Fboost%2Fmultiprecision%2Fcpp_df_qf%2Fcpp_df_qf_detail_ccmath_fma.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9tdWx0aXByZWNpc2lvbi9jcHBfZGZfcWYvY3BwX2RmX3FmX2RldGFpbF9jY21hdGhfZm1hLmhwcA==) | `100.0% <100.0%> (ø)` | |  
| [...cision/cpp\_df\_qf/cpp\_df\_qf\_detail\_ccmath\_frexp.hpp](https://app.codecov.io/gh/boostorg/multiprecision/pull/730?src=pr&el=tree&filepath=include%2Fboost%2Fmultiprecision%2Fcpp_df_qf%2Fcpp_df_qf_detail_ccmath_frexp.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9tdWx0aXByZWNpc2lvbi9jcHBfZGZfcWYvY3BwX2RmX3FmX2RldGFpbF9jY21hdGhfZnJleHAuaHBw) | `100.0% <100.0%> (ø)` | |  
| [...cision/cpp\_df\_qf/cpp\_df\_qf\_detail\_ccmath\_isnan.hpp](https://app.codecov.io/gh/boostorg/multiprecision/pull/730?src=pr&el=tree&filepath=include%2Fboost%2Fmultiprecision%2Fcpp_df_qf%2Fcpp_df_qf_detail_ccmath_isnan.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9tdWx0aXByZWNpc2lvbi9jcHBfZGZfcWYvY3BwX2RmX3FmX2RldGFpbF9jY21hdGhfaXNuYW4uaHBw) | `100.0% <100.0%> (ø)` | |  
| [...cision/cpp\_df\_qf/cpp\_df\_qf\_detail\_ccmath\_ldexp.hpp](https://app.codecov.io/gh/boostorg/multiprecision/pull/730?src=pr&el=tree&filepath=include%2Fboost%2Fmultiprecision%2Fcpp_df_qf%2Fcpp_df_qf_detail_ccmath_ldexp.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9tdWx0aXByZWNpc2lvbi9jcHBfZGZfcWYvY3BwX2RmX3FmX2RldGFpbF9jY21hdGhfbGRleHAuaHBw) | `100.0% <100.0%> (ø)` | |  
| [...recision/cpp\_df\_qf/cpp\_df\_qf\_detail\_ccmath\_log.hpp](https://app.codecov.io/gh/boostorg/multiprecision/pull/730?src=pr&el=tree&filepath=include%2Fboost%2Fmultiprecision%2Fcpp_df_qf%2Fcpp_df_qf_detail_ccmath_log.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9tdWx0aXByZWNpc2lvbi9jcHBfZGZfcWYvY3BwX2RmX3FmX2RldGFpbF9jY21hdGhfbG9nLmhwcA==) | `100.0% <100.0%> (ø)` | |  
| [...ecision/cpp\_df\_qf/cpp\_df\_qf\_detail\_ccmath\_sqrt.hpp](https://app.codecov.io/gh/boostorg/multiprecision/pull/730?src=pr&el=tree&filepath=include%2Fboost%2Fmultiprecision%2Fcpp_df_qf%2Fcpp_df_qf_detail_ccmath_sqrt.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9tdWx0aXByZWNpc2lvbi9jcHBfZGZfcWYvY3BwX2RmX3FmX2RldGFpbF9jY21hdGhfc3FydC5ocHA=) | `100.0% <100.0%> (ø)` | |  
| [...precision/cpp\_df\_qf/cpp\_df\_qf\_detail\_constants.hpp](https://app.codecov.io/gh/boostorg/multiprecision/pull/730?src=pr&el=tree&filepath=include%2Fboost%2Fmultiprecision%2Fcpp_df_qf%2Fcpp_df_qf_detail_constants.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9tdWx0aXByZWNpc2lvbi9jcHBfZGZfcWYvY3BwX2RmX3FmX2RldGFpbF9jb25zdGFudHMuaHBw) | `100.0% <100.0%> (ø)` | |  
| [include/boost/multiprecision/cpp\_double\_fp.hpp](https://app.codecov.io/gh/boostorg/multiprecision/pull/730?src=pr&el=tree&filepath=include%2Fboost%2Fmultiprecision%2Fcpp_double_fp.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9tdWx0aXByZWNpc2lvbi9jcHBfZG91YmxlX2ZwLmhwcA==) | `100.0% <100.0%> (ø)` | |  
| ... and [2 more](https://app.codecov.io/gh/boostorg/multiprecision/pull/730?src=pr&el=tree-more&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | |  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/multiprecision/pull/730?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/multiprecision/pull/730?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [fc508e8...4aa8952](https://app.codecov.io/gh/boostorg/multiprecision/pull/730?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
</details>  
<details><summary> :rocket: New features to boost your workflow: </summary>  
  
- :snowflake: [Test Analytics](https://docs.codecov.com/docs/test-analytics): Detect flaky tests, report on failures, and find test suite problems.  
</details>

---
