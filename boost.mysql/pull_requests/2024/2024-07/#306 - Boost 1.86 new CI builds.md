# #306 Boost 1.86 new CI builds [Merged]

> Username: anarthal  
> Created at: 2024-07-07 16:29:03 UTC  
> Updated at: 2024-07-08 15:12:46 UTC  
> Merged at: 2024-07-08 15:12:43 UTC  
> Closed at: 2024-07-08 15:12:43 UTC  
> Url: https://github.com/boostorg/mysql/pull/306  

Added gcc-14 build  
Updated mysql8 to MySQL 8.4.1  
Updated mariadb to MariaDB 11.4.2  
Updated some CMake builds to the latest compiler versions  
Updated GHA workflows to use Node 20 actions  
Updated deprecated ENV commands in Dockerfiles  
Removed debugging statements in OSX CI build  
DB entrypoints are now inline scripts in Dockerfiles  
  
close #302

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2024-07-08 14:08:27 UTC  
> Updated_at: 2024-07-08 14:18:33 UTC  
> Url: https://github.com/boostorg/mysql/pull/306#issuecomment-2214180370  

## [Codecov](https://app.codecov.io/gh/boostorg/mysql/pull/306?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
All modified and coverable lines are covered by tests :white_check_mark:  
> Project coverage is 98.33%. Comparing base [(`6c48d6c`)](https://app.codecov.io/gh/boostorg/mysql/commit/6c48d6c6a6f1628b4e1903bb7aca5429e72b831e?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) to head [(`2d7152c`)](https://app.codecov.io/gh/boostorg/mysql/commit/2d7152cd35cba32d57f0b64fcad5e0a0b37743bd?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/mysql/pull/306/graphs/tree.svg?width=650&height=150&src=pr&token=ajbbjm3OiO&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/mysql/pull/306?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #306      +/-   ##  
===========================================  
- Coverage    98.52%   98.33%   -0.19%       
===========================================  
  Files          134      129       -5       
  Lines         6488     5895     -593       
===========================================  
- Hits          6392     5797     -595       
- Misses          96       98       +2       
```  
  
[see 41 files with indirect coverage changes](https://app.codecov.io/gh/boostorg/mysql/pull/306/indirect-changes?src=pr&el=tree-more&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/mysql/pull/306?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/mysql/pull/306?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [6c48d6c...2d7152c](https://app.codecov.io/gh/boostorg/mysql/pull/306?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---
