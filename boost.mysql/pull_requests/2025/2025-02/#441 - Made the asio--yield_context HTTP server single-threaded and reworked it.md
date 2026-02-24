# #441 Made the asio::yield_context HTTP server single-threaded and reworked it [Merged]

> Username: anarthal  
> Created at: 2025-02-14 17:38:29 UTC  
> Updated at: 2025-02-14 20:14:23 UTC  
> Merged at: 2025-02-14 20:13:01 UTC  
> Closed at: 2025-02-14 20:13:01 UTC  
> Url: https://github.com/boostorg/mysql/pull/441  

The example is now much more legible  
The example no longer crashes on termination  
Renamed it to match the C++ standard it requires  
  
close #414

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2025-02-14 18:31:02 UTC  
> Updated_at: 2025-02-14 20:14:23 UTC  
> Url: https://github.com/boostorg/mysql/pull/441#issuecomment-2660010162  

## [Codecov](https://app.codecov.io/gh/boostorg/mysql/pull/441?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
All modified and coverable lines are covered by tests :white_check_mark:  
> Project coverage is 98.95%. Comparing base [(`b4365f3`)](https://app.codecov.io/gh/boostorg/mysql/commit/b4365f3254ba99b18124104dad8b2ba386057982?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) to head [(`3e3d5fd`)](https://app.codecov.io/gh/boostorg/mysql/commit/3e3d5fd3bf006c4bd1705b05d0ac5354959b493b?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> Report is 1 commits behind head on develop.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/mysql/pull/441/graphs/tree.svg?width=650&height=150&src=pr&token=ajbbjm3OiO&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/mysql/pull/441?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@           Coverage Diff            @@  
##           develop     #441   +/-   ##  
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
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/mysql/pull/441?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/mysql/pull/441?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [b4365f3...3e3d5fd](https://app.codecov.io/gh/boostorg/mysql/pull/441?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---
