# #324 Fuzzers now run normally and are more resilient to failures [Merged]

> Username: anarthal  
> Created at: 2024-07-24 09:19:35 UTC  
> Updated at: 2024-07-24 15:38:57 UTC  
> Merged at: 2024-07-24 15:38:55 UTC  
> Closed at: 2024-07-24 15:38:55 UTC  
> Url: https://github.com/boostorg/mysql/pull/324  

Updated build-clang17 and build-clang18 to use ubuntu:24.04  
Fix missing package in build-clang18  
Fuzzers now fail if OpenSSL can't be found  
  
close #323

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2024-07-24 10:04:44 UTC  
> Updated_at: 2024-07-24 11:05:39 UTC  
> Url: https://github.com/boostorg/mysql/pull/324#issuecomment-2247461436  

## [Codecov](https://app.codecov.io/gh/boostorg/mysql/pull/324?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
All modified and coverable lines are covered by tests :white_check_mark:  
> Project coverage is 98.48%. Comparing base [(`0198dad`)](https://app.codecov.io/gh/boostorg/mysql/commit/0198dad18f463bac73369fbc8211484ebfc4d28f?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) to head [(`d89ea13`)](https://app.codecov.io/gh/boostorg/mysql/commit/d89ea136d5a0dc25cc5b0a97e79cddc7708568d5?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/mysql/pull/324/graphs/tree.svg?width=650&height=150&src=pr&token=ajbbjm3OiO&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/mysql/pull/324?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@           Coverage Diff            @@  
##           develop     #324   +/-   ##  
========================================  
  Coverage    98.48%   98.48%             
========================================  
  Files          138      138             
  Lines         7059     7059             
========================================  
  Hits          6952     6952             
  Misses         107      107             
```  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/mysql/pull/324?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/mysql/pull/324?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [0198dad...d89ea13](https://app.codecov.io/gh/boostorg/mysql/pull/324?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---
