# #64 Switch to reusable GHA workflow [Merged]

> Username: Flamefire  
> Created at: 2025-09-30 17:44:49 UTC  
> Updated at: 2025-10-03 10:13:19 UTC  
> Merged at: 2025-10-03 10:12:05 UTC  
> Closed at: 2025-10-03 10:12:06 UTC  
> Url: https://github.com/boostorg/static_string/pull/64  

Avoid the current failures due to outdated CI configs

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2025-09-30 17:55:03 UTC  
> Url: https://github.com/boostorg/static_string/pull/64#issuecomment-3353240922  

## [Codecov](https://app.codecov.io/gh/boostorg/static_string/pull/64?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
:white_check_mark: All modified and coverable lines are covered by tests.  
:white_check_mark: Project coverage is 98.69%. Comparing base ([`20f2108`](https://app.codecov.io/gh/boostorg/static_string/commit/20f21083f794cea6bb18dc607523dc215ee24b7c?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)) to head ([`5cf3a2f`](https://app.codecov.io/gh/boostorg/static_string/commit/5cf3a2f9de19f482b3717e31a43a9e52ad13d1ae?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)).  
:warning: Report is 37 commits behind head on develop.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/static_string/pull/64/graphs/tree.svg?width=650&height=150&src=pr&token=LSsgELMWac&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/static_string/pull/64?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@             Coverage Diff             @@  
##           develop      #64      +/-   ##  
===========================================  
- Coverage   100.00%   98.69%   -1.31%       
===========================================  
  Files            1        1                
  Lines          862      993     +131       
  Branches         0      139     +139       
===========================================  
+ Hits           862      980     +118       
- Misses           0       12      +12       
- Partials         0        1       +1       
```  
[see 1 file with indirect coverage changes](https://app.codecov.io/gh/boostorg/static_string/pull/64/indirect-changes?src=pr&el=tree-more&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/static_string/pull/64?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/static_string/pull/64?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [7a53b7f...5cf3a2f](https://app.codecov.io/gh/boostorg/static_string/pull/64?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
</details>  
<details><summary> :rocket: New features to boost your workflow: </summary>  
  
- :snowflake: [Test Analytics](https://docs.codecov.com/docs/test-analytics): Detect flaky tests, report on failures, and find test suite problems.  
</details>

---

## Comment 2

> Username: Flamefire  
> Created_at: 2025-10-03 10:04:19 UTC  
> Url: https://github.com/boostorg/static_string/pull/64#issuecomment-3365117925  

I disabled C++2c/26 until #66 gets resolved

---
