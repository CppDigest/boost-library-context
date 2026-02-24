# #373 Document that ping_interval should be >= wait_timeout [Merged]

> Username: anarthal  
> Created at: 2024-10-14 14:11:50 UTC  
> Updated at: 2024-10-14 15:41:21 UTC  
> Merged at: 2024-10-14 15:41:08 UTC  
> Closed at: 2024-10-14 15:41:08 UTC  
> Url: https://github.com/boostorg/mysql/pull/373  

Fix defect in connection_pool docs  
  
close #290

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2024-10-14 15:02:22 UTC  
> Updated_at: 2024-10-14 15:41:21 UTC  
> Url: https://github.com/boostorg/mysql/pull/373#issuecomment-2411532475  

## [Codecov](https://app.codecov.io/gh/boostorg/mysql/pull/373?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
All modified and coverable lines are covered by tests :white_check_mark:  
> Project coverage is 99.04%. Comparing base [(`eba723f`)](https://app.codecov.io/gh/boostorg/mysql/commit/eba723f5a70183d523c63e9bb553a4eae8000cc2?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) to head [(`f987fbf`)](https://app.codecov.io/gh/boostorg/mysql/commit/f987fbf8e3b8fd484da6706f4cb9640c8e651139?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> Report is 1 commits behind head on develop.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/mysql/pull/373/graphs/tree.svg?width=650&height=150&src=pr&token=ajbbjm3OiO&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/mysql/pull/373?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@           Coverage Diff            @@  
##           develop     #373   +/-   ##  
========================================  
  Coverage    99.04%   99.04%             
========================================  
  Files          143      143             
  Lines         7157     7157             
========================================  
  Hits          7089     7089             
  Misses          68       68             
```  
  
| [Files with missing lines](https://app.codecov.io/gh/boostorg/mysql/pull/373?dropdown=coverage&src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/mysql/connection\_pool.hpp](https://app.codecov.io/gh/boostorg/mysql/pull/373?src=pr&el=tree&filepath=include%2Fboost%2Fmysql%2Fconnection_pool.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9teXNxbC9jb25uZWN0aW9uX3Bvb2wuaHBw) | `100.00% <ø> (ø)` | |  
| [include/boost/mysql/pool\_params.hpp](https://app.codecov.io/gh/boostorg/mysql/pull/373?src=pr&el=tree&filepath=include%2Fboost%2Fmysql%2Fpool_params.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9teXNxbC9wb29sX3BhcmFtcy5ocHA=) | `100.00% <ø> (ø)` | |  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/mysql/pull/373?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/mysql/pull/373?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [eba723f...f987fbf](https://app.codecov.io/gh/boostorg/mysql/pull/373?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---
