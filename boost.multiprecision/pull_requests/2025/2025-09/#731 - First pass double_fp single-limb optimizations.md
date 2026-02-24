# #731 First pass double_fp single-limb optimizations [Merged]

> Username: ckormanyos  
> Created at: 2025-09-01 19:45:07 UTC  
> Updated at: 2025-09-04 01:16:40 UTC  
> Merged at: 2025-09-03 19:44:16 UTC  
> Closed at: 2025-09-03 19:44:16 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/731  



---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2025-09-03 19:22:16 UTC  
> Updated_at: 2025-09-04 01:16:40 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/731#issuecomment-3250486222  

## [Codecov](https://app.codecov.io/gh/boostorg/multiprecision/pull/731?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
:white_check_mark: All modified and coverable lines are covered by tests.  
:white_check_mark: Project coverage is 96.2%. Comparing base ([`d6bef9f`](https://app.codecov.io/gh/boostorg/multiprecision/commit/d6bef9f5d26e42e7be5c4f6bafa16c46eca5eaab?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)) to head ([`cbcf51c`](https://app.codecov.io/gh/boostorg/multiprecision/commit/cbcf51c54a223463edc2769c332d10df31332339?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)).  
:warning: Report is 10 commits behind head on develop.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/multiprecision/pull/731/graphs/tree.svg?width=650&height=150&src=pr&token=SDDBym7Pc9&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/multiprecision/pull/731?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@            Coverage Diff            @@  
##           develop    #731     +/-   ##  
=========================================  
+ Coverage     96.2%   96.2%   +0.1%       
=========================================  
  Files          297     297               
  Lines        31190   31195      +5       
=========================================  
+ Hits         29978   29983      +5       
  Misses        1212    1212               
```  
  
| [Files with missing lines](https://app.codecov.io/gh/boostorg/multiprecision/pull/731?dropdown=coverage&src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [...oost/multiprecision/cpp\_df\_qf/cpp\_df\_qf\_detail.hpp](https://app.codecov.io/gh/boostorg/multiprecision/pull/731?src=pr&el=tree&filepath=include%2Fboost%2Fmultiprecision%2Fcpp_df_qf%2Fcpp_df_qf_detail.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9tdWx0aXByZWNpc2lvbi9jcHBfZGZfcWYvY3BwX2RmX3FmX2RldGFpbC5ocHA=) | `100.0% <100.0%> (ø)` | |  
| [include/boost/multiprecision/cpp\_double\_fp.hpp](https://app.codecov.io/gh/boostorg/multiprecision/pull/731?src=pr&el=tree&filepath=include%2Fboost%2Fmultiprecision%2Fcpp_double_fp.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9tdWx0aXByZWNpc2lvbi9jcHBfZG91YmxlX2ZwLmhwcA==) | `100.0% <100.0%> (ø)` | |  
| [test/test\_arithmetic\_df.cpp](https://app.codecov.io/gh/boostorg/multiprecision/pull/731?src=pr&el=tree&filepath=test%2Ftest_arithmetic_df.cpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC90ZXN0X2FyaXRobWV0aWNfZGYuY3Bw) | `100.0% <100.0%> (ø)` | |  
| [test/test\_round.cpp](https://app.codecov.io/gh/boostorg/multiprecision/pull/731?src=pr&el=tree&filepath=test%2Ftest_round.cpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC90ZXN0X3JvdW5kLmNwcA==) | `100.0% <ø> (ø)` | |  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/multiprecision/pull/731?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/multiprecision/pull/731?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [d6bef9f...cbcf51c](https://app.codecov.io/gh/boostorg/multiprecision/pull/731?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
</details>  
<details><summary> :rocket: New features to boost your workflow: </summary>  
  
- :snowflake: [Test Analytics](https://docs.codecov.com/docs/test-analytics): Detect flaky tests, report on failures, and find test suite problems.  
</details>

---
