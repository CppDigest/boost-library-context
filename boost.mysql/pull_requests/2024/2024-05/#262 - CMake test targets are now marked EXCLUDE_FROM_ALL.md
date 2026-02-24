# #262 CMake test targets are now marked EXCLUDE_FROM_ALL [Merged]

> Username: anarthal  
> Created at: 2024-05-15 17:13:24 UTC  
> Updated at: 2024-05-16 10:45:58 UTC  
> Merged at: 2024-05-16 10:45:57 UTC  
> Closed at: 2024-05-16 10:45:57 UTC  
> Url: https://github.com/boostorg/mysql/pull/262  

All test targets (including integration tests) are now only built when the `tests` target is built.  
Added a CMake build that doesn't run integration testing (verifying that BOOST_MYSQL_INTEGRATION_TESTS works)  
  
close #261

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2024-05-15 18:02:24 UTC  
> Updated_at: 2024-05-16 09:47:28 UTC  
> Url: https://github.com/boostorg/mysql/pull/262#issuecomment-2113137862  

## [Codecov](https://app.codecov.io/gh/boostorg/mysql/pull/262?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
All modified and coverable lines are covered by tests :white_check_mark:  
> Project coverage is 98.51%. Comparing base [(`77de54c`)](https://app.codecov.io/gh/boostorg/mysql/commit/77de54cf2c7d53919c974695dfe9cda9a008b5c9?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) to head [(`4024624`)](https://app.codecov.io/gh/boostorg/mysql/pull/262?dropdown=coverage&src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/mysql/pull/262/graphs/tree.svg?width=650&height=150&src=pr&token=ajbbjm3OiO&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/mysql/pull/262?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@           Coverage Diff            @@  
##           develop     #262   +/-   ##  
========================================  
  Coverage    98.51%   98.51%             
========================================  
  Files          132      132             
  Lines         6251     6251             
========================================  
  Hits          6158     6158             
  Misses          93       93             
```  
  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/mysql/pull/262?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/mysql/pull/262?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [77de54c...4024624](https://app.codecov.io/gh/boostorg/mysql/pull/262?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---
