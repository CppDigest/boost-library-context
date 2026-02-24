# #396 connection_pool now resizes correctly in the presence of pending connections [Merged]

> Username: anarthal  
> Created at: 2025-01-03 13:49:06 UTC  
> Updated at: 2025-01-06 16:16:55 UTC  
> Merged at: 2025-01-06 16:14:32 UTC  
> Closed at: 2025-01-06 16:14:32 UTC  
> Url: https://github.com/boostorg/mysql/pull/396  

Changed the algorithm that calculates how many connections to create to correctly take into account the number of pending requests.  
This makes the pool resize as expected and avoids potential deadlocks.  
  
close #395

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2025-01-03 14:41:40 UTC  
> Updated_at: 2025-01-06 16:16:55 UTC  
> Url: https://github.com/boostorg/mysql/pull/396#issuecomment-2569333667  

## [Codecov](https://app.codecov.io/gh/boostorg/mysql/pull/396?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
All modified and coverable lines are covered by tests :white_check_mark:  
> Project coverage is 98.95%. Comparing base [(`25f9900`)](https://app.codecov.io/gh/boostorg/mysql/commit/25f9900c0ba9b5f58d75ff86097782388979165a?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) to head [(`f2b8411`)](https://app.codecov.io/gh/boostorg/mysql/commit/f2b84115c48cb99c6a3d1764957c314c67d0d2ef?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> Report is 1 commits behind head on develop.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/mysql/pull/396/graphs/tree.svg?width=650&height=150&src=pr&token=ajbbjm3OiO&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/mysql/pull/396?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@           Coverage Diff            @@  
##           develop     #396   +/-   ##  
========================================  
  Coverage    98.95%   98.95%             
========================================  
  Files          142      142             
  Lines         7186     7199   +13       
========================================  
+ Hits          7111     7124   +13       
  Misses          75       75             
```  
  
| [Files with missing lines](https://app.codecov.io/gh/boostorg/mysql/pull/396?dropdown=coverage&src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [.../impl/internal/connection\_pool/connection\_node.hpp](https://app.codecov.io/gh/boostorg/mysql/pull/396?src=pr&el=tree&filepath=include%2Fboost%2Fmysql%2Fimpl%2Finternal%2Fconnection_pool%2Fconnection_node.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9teXNxbC9pbXBsL2ludGVybmFsL2Nvbm5lY3Rpb25fcG9vbC9jb25uZWN0aW9uX25vZGUuaHBw) | `98.87% <100.00%> (ø)` | |  
| [.../internal/connection\_pool/connection\_pool\_impl.hpp](https://app.codecov.io/gh/boostorg/mysql/pull/396?src=pr&el=tree&filepath=include%2Fboost%2Fmysql%2Fimpl%2Finternal%2Fconnection_pool%2Fconnection_pool_impl.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9teXNxbC9pbXBsL2ludGVybmFsL2Nvbm5lY3Rpb25fcG9vbC9jb25uZWN0aW9uX3Bvb2xfaW1wbC5ocHA=) | `98.89% <100.00%> (+0.03%)` | :arrow_up: |  
| [...nternal/connection\_pool/sansio\_connection\_node.hpp](https://app.codecov.io/gh/boostorg/mysql/pull/396?src=pr&el=tree&filepath=include%2Fboost%2Fmysql%2Fimpl%2Finternal%2Fconnection_pool%2Fsansio_connection_node.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9teXNxbC9pbXBsL2ludGVybmFsL2Nvbm5lY3Rpb25fcG9vbC9zYW5zaW9fY29ubmVjdGlvbl9ub2RlLmhwcA==) | `98.76% <100.00%> (+0.11%)` | :arrow_up: |  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/mysql/pull/396?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/mysql/pull/396?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [25f9900...f2b8411](https://app.codecov.io/gh/boostorg/mysql/pull/396?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---
