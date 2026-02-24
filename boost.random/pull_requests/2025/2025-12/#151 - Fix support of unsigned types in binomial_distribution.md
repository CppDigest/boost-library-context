# #151 Fix support of unsigned types in binomial_distribution [Merged]

> Username: mborland  
> Created at: 2025-12-02 11:42:37 UTC  
> Updated at: 2025-12-02 13:55:03 UTC  
> Merged at: 2025-12-02 13:54:16 UTC  
> Closed at: 2025-12-02 13:54:16 UTC  
> Url: https://github.com/boostorg/random/pull/151  

Fixes: #150

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2025-12-02 12:02:02 UTC  
> Updated_at: 2025-12-02 13:55:03 UTC  
> Url: https://github.com/boostorg/random/pull/151#issuecomment-3601687788  

## [Codecov](https://app.codecov.io/gh/boostorg/random/pull/151?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
:white_check_mark: All modified and coverable lines are covered by tests.  
:white_check_mark: Project coverage is 95.79%. Comparing base ([`525a07c`](https://app.codecov.io/gh/boostorg/random/commit/525a07cb70b53b14b707669308fcc96dee938713?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)) to head ([`88d6840`](https://app.codecov.io/gh/boostorg/random/commit/88d6840a19455fe0e6ecd18fb8be48e4de2cf420?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)).  
:warning: Report is 3 commits behind head on develop.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/random/pull/151/graphs/tree.svg?width=650&height=150&src=pr&token=7wvW4uAjLp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/random/pull/151?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@           Coverage Diff            @@  
##           develop     #151   +/-   ##  
========================================  
  Coverage    95.79%   95.79%             
========================================  
  Files          116      117    +1       
  Lines         7659     7664    +5       
========================================  
+ Hits          7337     7342    +5       
  Misses         322      322             
```  
  
| [Files with missing lines](https://app.codecov.io/gh/boostorg/random/pull/151?dropdown=coverage&src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/random/binomial\_distribution.hpp](https://app.codecov.io/gh/boostorg/random/pull/151?src=pr&el=tree&filepath=include%2Fboost%2Frandom%2Fbinomial_distribution.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9yYW5kb20vYmlub21pYWxfZGlzdHJpYnV0aW9uLmhwcA==) | `97.84% <100.00%> (ø)` | |  
| [test/github\_issue\_150.cpp](https://app.codecov.io/gh/boostorg/random/pull/151?src=pr&el=tree&filepath=test%2Fgithub_issue_150.cpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC9naXRodWJfaXNzdWVfMTUwLmNwcA==) | `100.00% <100.00%> (ø)` | |  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/random/pull/151?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/random/pull/151?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [525a07c...88d6840](https://app.codecov.io/gh/boostorg/random/pull/151?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
</details>  
<details><summary> :rocket: New features to boost your workflow: </summary>  
  
- :snowflake: [Test Analytics](https://docs.codecov.com/docs/test-analytics): Detect flaky tests, report on failures, and find test suite problems.  
</details>

---
