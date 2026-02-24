# #735 Handle Git issue 734 in docs and code [Merged]

> Username: ckormanyos  
> Created at: 2025-09-06 08:22:14 UTC  
> Updated at: 2025-09-06 17:03:44 UTC  
> Merged at: 2025-09-06 15:39:43 UTC  
> Closed at: 2025-09-06 15:39:43 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/735  



---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2025-09-06 11:07:58 UTC  
> Updated_at: 2025-09-06 15:39:51 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/735#issuecomment-3261876158  

## [Codecov](https://app.codecov.io/gh/boostorg/multiprecision/pull/735?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
:white_check_mark: All modified and coverable lines are covered by tests.  
:white_check_mark: Project coverage is 96.2%. Comparing base ([`715e0b4`](https://app.codecov.io/gh/boostorg/multiprecision/commit/715e0b461e4c4f9771483c5b1bd8b900718779e8?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)) to head ([`db2bcea`](https://app.codecov.io/gh/boostorg/multiprecision/commit/db2bcea38516eb183ff21c491793489d88c056a5?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)).  
:warning: Report is 5 commits behind head on develop.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/multiprecision/pull/735/graphs/tree.svg?width=650&height=150&src=pr&token=SDDBym7Pc9&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/multiprecision/pull/735?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@            Coverage Diff            @@  
##           develop    #735     +/-   ##  
=========================================  
+ Coverage     96.2%   96.2%   +0.1%       
=========================================  
  Files          297     298      +1       
  Lines        31212   31242     +30       
=========================================  
+ Hits         30002   30031     +29       
- Misses        1210    1211      +1       
```  
  
| [Files with missing lines](https://app.codecov.io/gh/boostorg/multiprecision/pull/735?dropdown=coverage&src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/multiprecision/cpp\_dec\_float.hpp](https://app.codecov.io/gh/boostorg/multiprecision/pull/735?src=pr&el=tree&filepath=include%2Fboost%2Fmultiprecision%2Fcpp_dec_float.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9tdWx0aXByZWNpc2lvbi9jcHBfZGVjX2Zsb2F0LmhwcA==) | `99.8% <100.0%> (+0.1%)` | :arrow_up: |  
| [include/boost/multiprecision/cpp\_double\_fp.hpp](https://app.codecov.io/gh/boostorg/multiprecision/pull/735?src=pr&el=tree&filepath=include%2Fboost%2Fmultiprecision%2Fcpp_double_fp.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9tdWx0aXByZWNpc2lvbi9jcHBfZG91YmxlX2ZwLmhwcA==) | `100.0% <ø> (ø)` | |  
| [test/git\_issue\_734.cpp](https://app.codecov.io/gh/boostorg/multiprecision/pull/735?src=pr&el=tree&filepath=test%2Fgit_issue_734.cpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC9naXRfaXNzdWVfNzM0LmNwcA==) | `100.0% <100.0%> (ø)` | |  
  
... and [2 files with indirect coverage changes](https://app.codecov.io/gh/boostorg/multiprecision/pull/735/indirect-changes?src=pr&el=tree-more&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/multiprecision/pull/735?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/multiprecision/pull/735?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [715e0b4...db2bcea](https://app.codecov.io/gh/boostorg/multiprecision/pull/735?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
</details>  
<details><summary> :rocket: New features to boost your workflow: </summary>  
  
- :snowflake: [Test Analytics](https://docs.codecov.com/docs/test-analytics): Detect flaky tests, report on failures, and find test suite problems.  
</details>

---
