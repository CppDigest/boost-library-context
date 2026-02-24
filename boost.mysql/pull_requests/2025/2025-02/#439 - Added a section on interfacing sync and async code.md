# #439 Added a section on interfacing sync and async code [Merged]

> Username: anarthal  
> Created at: 2025-02-13 19:03:16 UTC  
> Updated at: 2025-02-14 13:03:36 UTC  
> Merged at: 2025-02-14 13:03:03 UTC  
> Closed at: 2025-02-14 13:03:04 UTC  
> Url: https://github.com/boostorg/mysql/pull/439  

Replaces an incorrect sync connection_pool snippet that contained a race condition.  
  
close #412

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2025-02-13 19:55:49 UTC  
> Updated_at: 2025-02-14 13:03:36 UTC  
> Url: https://github.com/boostorg/mysql/pull/439#issuecomment-2657583146  

## [Codecov](https://app.codecov.io/gh/boostorg/mysql/pull/439?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
All modified and coverable lines are covered by tests :white_check_mark:  
> Project coverage is 98.95%. Comparing base [(`5a3a2f4`)](https://app.codecov.io/gh/boostorg/mysql/commit/5a3a2f40c917fada58a6e963f244c07b9577ccc0?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) to head [(`f6ffe57`)](https://app.codecov.io/gh/boostorg/mysql/commit/f6ffe576c48a0ada9a80545d2c7d8d8f3cf8dc03?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> Report is 2 commits behind head on develop.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/mysql/pull/439/graphs/tree.svg?width=650&height=150&src=pr&token=ajbbjm3OiO&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/mysql/pull/439?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@           Coverage Diff            @@  
##           develop     #439   +/-   ##  
========================================  
  Coverage    98.95%   98.95%             
========================================  
  Files          142      142             
  Lines         7192     7192             
========================================  
  Hits          7117     7117             
  Misses          75       75             
```  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/mysql/pull/439?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/mysql/pull/439?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [5a3a2f4...f6ffe57](https://app.codecov.io/gh/boostorg/mysql/pull/439?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---
