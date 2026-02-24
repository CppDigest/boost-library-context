# #354 Removed pool executor params [Merged]

> Username: anarthal  
> Created at: 2024-09-27 13:39:00 UTC  
> Updated at: 2024-10-03 04:28:52 UTC  
> Merged at: 2024-10-03 04:28:44 UTC  
> Closed at: 2024-10-03 04:28:44 UTC  
> Url: https://github.com/boostorg/mysql/pull/354  

Added pool_params::connection_executor  
  
close #350

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2024-09-30 20:39:55 UTC  
> Updated_at: 2024-10-03 04:28:52 UTC  
> Url: https://github.com/boostorg/mysql/pull/354#issuecomment-2384105537  

## [Codecov](https://app.codecov.io/gh/boostorg/mysql/pull/354?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
All modified and coverable lines are covered by tests :white_check_mark:  
> Project coverage is 99.05%. Comparing base [(`a4a40d8`)](https://app.codecov.io/gh/boostorg/mysql/commit/a4a40d8b90db09be84383c90fc5e2f158be45197?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) to head [(`82396e1`)](https://app.codecov.io/gh/boostorg/mysql/commit/82396e177ba9a855ff82646928af14cb9b65a2de?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> Report is 1 commits behind head on develop.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/mysql/pull/354/graphs/tree.svg?width=650&height=150&src=pr&token=ajbbjm3OiO&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/mysql/pull/354?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #354      +/-   ##  
===========================================  
- Coverage    99.05%   99.05%   -0.01%       
===========================================  
  Files          143      143                
  Lines         7184     7180       -4       
===========================================  
- Hits          7116     7112       -4       
  Misses          68       68                
```  
  
| [Files with missing lines](https://app.codecov.io/gh/boostorg/mysql/pull/354?dropdown=coverage&src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/mysql/connection\_pool.hpp](https://app.codecov.io/gh/boostorg/mysql/pull/354?src=pr&el=tree&filepath=include%2Fboost%2Fmysql%2Fconnection_pool.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9teXNxbC9jb25uZWN0aW9uX3Bvb2wuaHBw) | `100.00% <100.00%> (ø)` | |  
| [include/boost/mysql/impl/connection\_pool.ipp](https://app.codecov.io/gh/boostorg/mysql/pull/354?src=pr&el=tree&filepath=include%2Fboost%2Fmysql%2Fimpl%2Fconnection_pool.ipp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9teXNxbC9pbXBsL2Nvbm5lY3Rpb25fcG9vbC5pcHA=) | `100.00% <100.00%> (ø)` | |  
| [.../internal/connection\_pool/connection\_pool\_impl.hpp](https://app.codecov.io/gh/boostorg/mysql/pull/354?src=pr&el=tree&filepath=include%2Fboost%2Fmysql%2Fimpl%2Finternal%2Fconnection_pool%2Fconnection_pool_impl.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9teXNxbC9pbXBsL2ludGVybmFsL2Nvbm5lY3Rpb25fcG9vbC9jb25uZWN0aW9uX3Bvb2xfaW1wbC5ocHA=) | `98.86% <100.00%> (-0.01%)` | :arrow_down: |  
| [include/boost/mysql/pool\_params.hpp](https://app.codecov.io/gh/boostorg/mysql/pull/354?src=pr&el=tree&filepath=include%2Fboost%2Fmysql%2Fpool_params.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9teXNxbC9wb29sX3BhcmFtcy5ocHA=) | `100.00% <ø> (ø)` | |  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/mysql/pull/354?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/mysql/pull/354?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [a4a40d8...82396e1](https://app.codecov.io/gh/boostorg/mysql/pull/354?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---
