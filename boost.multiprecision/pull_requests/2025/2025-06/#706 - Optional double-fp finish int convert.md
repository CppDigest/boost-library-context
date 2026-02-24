# #706 Optional double-fp finish int convert [Merged]

> Username: ckormanyos  
> Created at: 2025-06-29 19:29:01 UTC  
> Updated at: 2025-06-30 11:42:57 UTC  
> Merged at: 2025-06-30 11:42:50 UTC  
> Closed at: 2025-06-30 11:42:50 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/706  

This one is optional, as I am going for the last 100 percent cover line in new double.fp backend.

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2025-06-30 08:03:40 UTC  
> Updated_at: 2025-06-30 11:42:57 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/706#issuecomment-3018186000  

## [Codecov](https://app.codecov.io/gh/boostorg/multiprecision/pull/706?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
All modified and coverable lines are covered by tests :white_check_mark:  
> Project coverage is 94.6%. Comparing base [(`d57c738`)](https://app.codecov.io/gh/boostorg/multiprecision/commit/d57c7383835459cccb28659bc5574b6ad0ce5a48?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) to head [(`b9331ec`)](https://app.codecov.io/gh/boostorg/multiprecision/commit/b9331ec3ebe5acd3f1c547c1c5c62be70388bd16?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> Report is 7 commits behind head on develop.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/multiprecision/pull/706/graphs/tree.svg?width=650&height=150&src=pr&token=SDDBym7Pc9&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/multiprecision/pull/706?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@            Coverage Diff            @@  
##           develop    #706     +/-   ##  
=========================================  
+ Coverage     94.6%   94.6%   +0.1%       
=========================================  
  Files          295     295               
  Lines        30687   30728     +41       
=========================================  
+ Hits         29000   29044     +44       
+ Misses        1687    1684      -3       
```  
  
| [Files with missing lines](https://app.codecov.io/gh/boostorg/multiprecision/pull/706?dropdown=coverage&src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/multiprecision/cpp\_double\_fp.hpp](https://app.codecov.io/gh/boostorg/multiprecision/pull/706?src=pr&el=tree&filepath=include%2Fboost%2Fmultiprecision%2Fcpp_double_fp.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9tdWx0aXByZWNpc2lvbi9jcHBfZG91YmxlX2ZwLmhwcA==) | `100.0% <100.0%> (+0.2%)` | :arrow_up: |  
| [test/test.hpp](https://app.codecov.io/gh/boostorg/multiprecision/pull/706?src=pr&el=tree&filepath=test%2Ftest.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC90ZXN0LmhwcA==) | `60.7% <ø> (ø)` | |  
| [test/test\_various\_edges.cpp](https://app.codecov.io/gh/boostorg/multiprecision/pull/706?src=pr&el=tree&filepath=test%2Ftest_various_edges.cpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC90ZXN0X3ZhcmlvdXNfZWRnZXMuY3Bw) | `100.0% <100.0%> (ø)` | |  
  
... and [1 file with indirect coverage changes](https://app.codecov.io/gh/boostorg/multiprecision/pull/706/indirect-changes?src=pr&el=tree-more&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/multiprecision/pull/706?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/multiprecision/pull/706?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [d57c738...b9331ec](https://app.codecov.io/gh/boostorg/multiprecision/pull/706?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>  
<details><summary> :rocket: New features to boost your workflow: </summary>  
  
- :snowflake: [Test Analytics](https://docs.codecov.com/docs/test-analytics): Detect flaky tests, report on failures, and find test suite problems.  
</details>

---
