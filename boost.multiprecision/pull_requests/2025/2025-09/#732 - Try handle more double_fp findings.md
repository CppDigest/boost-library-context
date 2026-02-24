# #732 Try handle more double_fp findings [Merged]

> Username: ckormanyos  
> Created at: 2025-09-04 09:50:06 UTC  
> Updated at: 2025-09-05 05:39:11 UTC  
> Merged at: 2025-09-05 05:38:59 UTC  
> Closed at: 2025-09-05 05:38:59 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/732  



---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2025-09-04 16:49:56 UTC  
> Updated_at: 2025-09-05 05:39:11 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/732#issuecomment-3254587939  

## [Codecov](https://app.codecov.io/gh/boostorg/multiprecision/pull/732?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
:white_check_mark: All modified and coverable lines are covered by tests.  
:white_check_mark: Project coverage is 96.2%. Comparing base ([`43ce2d3`](https://app.codecov.io/gh/boostorg/multiprecision/commit/43ce2d31497ac22f2c35d064bbc68ff377dcbcb6?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)) to head ([`f54b239`](https://app.codecov.io/gh/boostorg/multiprecision/commit/f54b239049d9dc697b87be2f523ea263fcd655e7?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)).  
:warning: Report is 7 commits behind head on develop.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/multiprecision/pull/732/graphs/tree.svg?width=650&height=150&src=pr&token=SDDBym7Pc9&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/multiprecision/pull/732?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@            Coverage Diff            @@  
##           develop    #732     +/-   ##  
=========================================  
+ Coverage     96.2%   96.2%   +0.1%       
=========================================  
  Files          297     297               
  Lines        31195   31212     +17       
=========================================  
+ Hits         29983   30000     +17       
  Misses        1212    1212               
```  
  
| [Files with missing lines](https://app.codecov.io/gh/boostorg/multiprecision/pull/732?dropdown=coverage&src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [...oost/multiprecision/cpp\_df\_qf/cpp\_df\_qf\_detail.hpp](https://app.codecov.io/gh/boostorg/multiprecision/pull/732?src=pr&el=tree&filepath=include%2Fboost%2Fmultiprecision%2Fcpp_df_qf%2Fcpp_df_qf_detail.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9tdWx0aXByZWNpc2lvbi9jcHBfZGZfcWYvY3BwX2RmX3FmX2RldGFpbC5ocHA=) | `100.0% <100.0%> (ø)` | |  
| [...recision/cpp\_df\_qf/cpp\_df\_qf\_detail\_ccmath\_log.hpp](https://app.codecov.io/gh/boostorg/multiprecision/pull/732?src=pr&el=tree&filepath=include%2Fboost%2Fmultiprecision%2Fcpp_df_qf%2Fcpp_df_qf_detail_ccmath_log.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9tdWx0aXByZWNpc2lvbi9jcHBfZGZfcWYvY3BwX2RmX3FmX2RldGFpbF9jY21hdGhfbG9nLmhwcA==) | `100.0% <ø> (ø)` | |  
| [include/boost/multiprecision/cpp\_double\_fp.hpp](https://app.codecov.io/gh/boostorg/multiprecision/pull/732?src=pr&el=tree&filepath=include%2Fboost%2Fmultiprecision%2Fcpp_double_fp.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9tdWx0aXByZWNpc2lvbi9jcHBfZG91YmxlX2ZwLmhwcA==) | `100.0% <100.0%> (ø)` | |  
| [test/test\_various\_edges\_more.cpp](https://app.codecov.io/gh/boostorg/multiprecision/pull/732?src=pr&el=tree&filepath=test%2Ftest_various_edges_more.cpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC90ZXN0X3ZhcmlvdXNfZWRnZXNfbW9yZS5jcHA=) | `100.0% <100.0%> (ø)` | |  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/multiprecision/pull/732?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/multiprecision/pull/732?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [43ce2d3...f54b239](https://app.codecov.io/gh/boostorg/multiprecision/pull/732?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
</details>  
<details><summary> :rocket: New features to boost your workflow: </summary>  
  
- :snowflake: [Test Analytics](https://docs.codecov.com/docs/test-analytics): Detect flaky tests, report on failures, and find test suite problems.  
</details>

---
