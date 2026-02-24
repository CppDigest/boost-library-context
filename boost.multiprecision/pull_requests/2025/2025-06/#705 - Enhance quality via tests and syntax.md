# #705 Enhance quality via tests and syntax [Merged]

> Username: ckormanyos  
> Created at: 2025-06-29 08:36:11 UTC  
> Updated at: 2025-06-29 19:32:00 UTC  
> Merged at: 2025-06-29 19:10:23 UTC  
> Closed at: 2025-06-29 19:10:23 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/705  

The purpose of this PR is to enhance quality via more tests and syntax. Some motivation comes from the new `cpp_double_fp_backend` backend.

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2025-06-29 10:44:39 UTC  
> Updated_at: 2025-06-29 19:32:00 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/705#issuecomment-3016580370  

## [Codecov](https://app.codecov.io/gh/boostorg/multiprecision/pull/705?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
All modified and coverable lines are covered by tests :white_check_mark:  
> Project coverage is 94.6%. Comparing base [(`fcb1f6a`)](https://app.codecov.io/gh/boostorg/multiprecision/commit/fcb1f6a6b4eaa4488e419ce0f2c2e1fdd04c2cc2?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) to head [(`8835179`)](https://app.codecov.io/gh/boostorg/multiprecision/commit/88351798f28d3d51b58a404e1ef23b003c8cd87d?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> Report is 9 commits behind head on develop.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/multiprecision/pull/705/graphs/tree.svg?width=650&height=150&src=pr&token=SDDBym7Pc9&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/multiprecision/pull/705?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@            Coverage Diff            @@  
##           develop    #705     +/-   ##  
=========================================  
+ Coverage     94.5%   94.6%   +0.1%       
=========================================  
  Files          295     295               
  Lines        30492   30687    +195       
=========================================  
+ Hits         28788   29000    +212       
+ Misses        1704    1687     -17       
```  
  
| [Files with missing lines](https://app.codecov.io/gh/boostorg/multiprecision/pull/705?dropdown=coverage&src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/multiprecision/cpp\_double\_fp.hpp](https://app.codecov.io/gh/boostorg/multiprecision/pull/705?src=pr&el=tree&filepath=include%2Fboost%2Fmultiprecision%2Fcpp_double_fp.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9tdWx0aXByZWNpc2lvbi9jcHBfZG91YmxlX2ZwLmhwcA==) | `99.9% <100.0%> (+1.3%)` | :arrow_up: |  
| [test/test\_pow.cpp](https://app.codecov.io/gh/boostorg/multiprecision/pull/705?src=pr&el=tree&filepath=test%2Ftest_pow.cpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC90ZXN0X3Bvdy5jcHA=) | `98.9% <100.0%> (+0.6%)` | :arrow_up: |  
| [test/test\_various\_edges.cpp](https://app.codecov.io/gh/boostorg/multiprecision/pull/705?src=pr&el=tree&filepath=test%2Ftest_various_edges.cpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC90ZXN0X3ZhcmlvdXNfZWRnZXMuY3Bw) | `100.0% <100.0%> (ø)` | |  
  
... and [3 files with indirect coverage changes](https://app.codecov.io/gh/boostorg/multiprecision/pull/705/indirect-changes?src=pr&el=tree-more&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/multiprecision/pull/705?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/multiprecision/pull/705?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [fcb1f6a...8835179](https://app.codecov.io/gh/boostorg/multiprecision/pull/705?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>  
<details><summary> :rocket: New features to boost your workflow: </summary>  
  
- :snowflake: [Test Analytics](https://docs.codecov.com/docs/test-analytics): Detect flaky tests, report on failures, and find test suite problems.  
</details>

---
