# #312 Improved server feature detection for integration tests [Merged]

> Username: anarthal  
> Created at: 2024-07-10 11:51:35 UTC  
> Updated at: 2024-07-11 15:36:44 UTC  
> Merged at: 2024-07-11 15:36:42 UTC  
> Closed at: 2024-07-11 15:36:42 UTC  
> Url: https://github.com/boostorg/mysql/pull/312  

Introduced BOOST_MYSQL_DISABLED_SERVER_FEATURES env var  
Removed BOOST_MYSQL_NO_UNIX_SOCKET_TESTS env var  
JSON tests in database_types are now actually run

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2024-07-11 14:42:47 UTC  
> Updated_at: 2024-07-11 15:35:49 UTC  
> Url: https://github.com/boostorg/mysql/pull/312#issuecomment-2223122805  

## [Codecov](https://app.codecov.io/gh/boostorg/mysql/pull/312?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
All modified and coverable lines are covered by tests :white_check_mark:  
> Project coverage is 98.35%. Comparing base [(`3faf294`)](https://app.codecov.io/gh/boostorg/mysql/commit/3faf2947f9951bb10239cb1c34cae9c571133d3e?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) to head [(`b65e0b5`)](https://app.codecov.io/gh/boostorg/mysql/commit/b65e0b55b2190d3cb4ec9867de04ecb98edcf640?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/mysql/pull/312/graphs/tree.svg?width=650&height=150&src=pr&token=ajbbjm3OiO&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/mysql/pull/312?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #312      +/-   ##  
===========================================  
+ Coverage    98.33%   98.35%   +0.01%       
===========================================  
  Files          129      129                
  Lines         5895     5894       -1       
===========================================  
  Hits          5797     5797                
+ Misses          98       97       -1       
```  
  
[see 1 file with indirect coverage changes](https://app.codecov.io/gh/boostorg/mysql/pull/312/indirect-changes?src=pr&el=tree-more&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/mysql/pull/312?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/mysql/pull/312?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [3faf294...b65e0b5](https://app.codecov.io/gh/boostorg/mysql/pull/312?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---
