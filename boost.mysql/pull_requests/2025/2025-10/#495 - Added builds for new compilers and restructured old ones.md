# #495 Added builds for new compilers and restructured old ones [Merged]

> Username: anarthal  
> Created at: 2025-10-06 16:10:44 UTC  
> Updated at: 2025-10-07 17:26:02 UTC  
> Merged at: 2025-10-07 12:41:04 UTC  
> Closed at: 2025-10-07 12:41:04 UTC  
> Url: https://github.com/boostorg/mysql/pull/495  

* Added coverage to fill the gaps between compiler versions for clang and gcc under Linux  
* Added clang-19 and clang-20 builds  
* Added a gcc-15 build  
* Replaced the old clang 3.6 build by clang 3.8  
  
close #478   
close #477   
close #476   
close #472

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2025-10-07 11:34:47 UTC  
> Updated_at: 2025-10-07 17:26:02 UTC  
> Url: https://github.com/boostorg/mysql/pull/495#issuecomment-3376488724  

## [Codecov](https://app.codecov.io/gh/boostorg/mysql/pull/495?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
:white_check_mark: All modified and coverable lines are covered by tests.  
:white_check_mark: Project coverage is 99.15%. Comparing base ([`0e5f42a`](https://app.codecov.io/gh/boostorg/mysql/commit/0e5f42aea93f923afcdb4e7780d6ae838739f34f?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)) to head ([`cda0446`](https://app.codecov.io/gh/boostorg/mysql/commit/cda04460201654482e16d125b7610760c335fca7?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)).  
:warning: Report is 2 commits behind head on develop.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/mysql/pull/495/graphs/tree.svg?width=650&height=150&src=pr&token=ajbbjm3OiO&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/mysql/pull/495?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@           Coverage Diff            @@  
##           develop     #495   +/-   ##  
========================================  
  Coverage    99.15%   99.15%             
========================================  
  Files          144      144             
  Lines         7370     7370             
========================================  
  Hits          7308     7308             
  Misses          62       62             
```  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/mysql/pull/495?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/mysql/pull/495?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [0e5f42a...cda0446](https://app.codecov.io/gh/boostorg/mysql/pull/495?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
</details>  
<details><summary> :rocket: New features to boost your workflow: </summary>  
  
- :snowflake: [Test Analytics](https://docs.codecov.com/docs/test-analytics): Detect flaky tests, report on failures, and find test suite problems.  
</details>

---
