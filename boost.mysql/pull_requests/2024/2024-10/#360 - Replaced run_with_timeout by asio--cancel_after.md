# #360 Replaced run_with_timeout by asio::cancel_after [Merged]

> Username: anarthal  
> Created at: 2024-10-03 19:32:12 UTC  
> Updated at: 2024-10-04 09:35:10 UTC  
> Merged at: 2024-10-04 09:35:02 UTC  
> Closed at: 2024-10-04 09:35:02 UTC  
> Url: https://github.com/boostorg/mysql/pull/360  

close #351

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2024-10-03 20:21:58 UTC  
> Updated_at: 2024-10-04 09:35:10 UTC  
> Url: https://github.com/boostorg/mysql/pull/360#issuecomment-2392262838  

## [Codecov](https://app.codecov.io/gh/boostorg/mysql/pull/360?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
All modified and coverable lines are covered by tests :white_check_mark:  
> Project coverage is 99.04%. Comparing base [(`268aa33`)](https://app.codecov.io/gh/boostorg/mysql/commit/268aa33a841b120b7cb65bb101184fc6d5df6d2f?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) to head [(`9d91b72`)](https://app.codecov.io/gh/boostorg/mysql/commit/9d91b7206bee771dbd692379dc6a8e4247a866c2?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> Report is 1 commits behind head on develop.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/mysql/pull/360/graphs/tree.svg?width=650&height=150&src=pr&token=ajbbjm3OiO&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/mysql/pull/360?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #360      +/-   ##  
===========================================  
- Coverage    99.05%   99.04%   -0.01%       
===========================================  
  Files          143      142       -1       
  Lines         7180     7142      -38       
===========================================  
- Hits          7112     7074      -38       
  Misses          68       68                
```  
  
| [Files with missing lines](https://app.codecov.io/gh/boostorg/mysql/pull/360?dropdown=coverage&src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [.../impl/internal/connection\_pool/connection\_node.hpp](https://app.codecov.io/gh/boostorg/mysql/pull/360?src=pr&el=tree&filepath=include%2Fboost%2Fmysql%2Fimpl%2Finternal%2Fconnection_pool%2Fconnection_node.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9teXNxbC9pbXBsL2ludGVybmFsL2Nvbm5lY3Rpb25fcG9vbC9jb25uZWN0aW9uX25vZGUuaHBw) | `98.68% <100.00%> (-0.05%)` | :arrow_down: |  
| [.../internal/connection\_pool/connection\_pool\_impl.hpp](https://app.codecov.io/gh/boostorg/mysql/pull/360?src=pr&el=tree&filepath=include%2Fboost%2Fmysql%2Fimpl%2Finternal%2Fconnection_pool%2Fconnection_pool_impl.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9teXNxbC9pbXBsL2ludGVybmFsL2Nvbm5lY3Rpb25fcG9vbC9jb25uZWN0aW9uX3Bvb2xfaW1wbC5ocHA=) | `98.86% <100.00%> (ø)` | |  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/mysql/pull/360?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/mysql/pull/360?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [268aa33...9d91b72](https://app.codecov.io/gh/boostorg/mysql/pull/360?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---
