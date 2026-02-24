# #394 Fixed a race condition in Windows CI when running the example servers [Merged]

> Username: anarthal  
> Created at: 2025-01-01 19:20:34 UTC  
> Updated at: 2025-01-01 20:23:20 UTC  
> Merged at: 2025-01-01 20:23:14 UTC  
> Closed at: 2025-01-01 20:23:14 UTC  
> Url: https://github.com/boostorg/mysql/pull/394  

Refactored launch_server into a common utility  
launch_server now uses `Popen.terminate()`, which handles the race condition case  
  
close #393

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2025-01-01 20:09:54 UTC  
> Updated_at: 2025-01-01 20:23:20 UTC  
> Url: https://github.com/boostorg/mysql/pull/394#issuecomment-2567131349  

## [Codecov](https://app.codecov.io/gh/boostorg/mysql/pull/394?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
All modified and coverable lines are covered by tests :white_check_mark:  
> Project coverage is 98.95%. Comparing base [(`c438f26`)](https://app.codecov.io/gh/boostorg/mysql/commit/c438f26731e36c2db6457705ec5dbb9f7657db2a?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) to head [(`13ae33d`)](https://app.codecov.io/gh/boostorg/mysql/commit/13ae33ddd1b97949daa24e80445db8fc9dcc892f?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> Report is 1 commits behind head on develop.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/mysql/pull/394/graphs/tree.svg?width=650&height=150&src=pr&token=ajbbjm3OiO&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/mysql/pull/394?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@           Coverage Diff            @@  
##           develop     #394   +/-   ##  
========================================  
  Coverage    98.95%   98.95%             
========================================  
  Files          142      142             
  Lines         7186     7186             
========================================  
  Hits          7111     7111             
  Misses          75       75             
```  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/mysql/pull/394?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/mysql/pull/394?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [c438f26...13ae33d](https://app.codecov.io/gh/boostorg/mysql/pull/394?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---
