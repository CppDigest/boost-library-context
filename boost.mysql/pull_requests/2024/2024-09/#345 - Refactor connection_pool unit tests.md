# #345 Refactor connection_pool unit tests [Merged]

> Username: anarthal  
> Created at: 2024-09-16 07:31:09 UTC  
> Updated at: 2024-09-16 15:33:25 UTC  
> Merged at: 2024-09-16 15:32:42 UTC  
> Closed at: 2024-09-16 15:32:42 UTC  
> Url: https://github.com/boostorg/mysql/pull/345  

Remove IoTraits  
mock_timer is now a specialization of asio::basic_waitable_timer

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2024-09-16 08:23:53 UTC  
> Updated_at: 2024-09-16 15:33:25 UTC  
> Url: https://github.com/boostorg/mysql/pull/345#issuecomment-2352288044  

## [Codecov](https://app.codecov.io/gh/boostorg/mysql/pull/345?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
All modified and coverable lines are covered by tests :white_check_mark:  
> Project coverage is 99.03%. Comparing base [(`1f79e5c`)](https://app.codecov.io/gh/boostorg/mysql/commit/1f79e5ce3f28df761d4ae7de298c8615a4a8b17f?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) to head [(`1854de1`)](https://app.codecov.io/gh/boostorg/mysql/commit/1854de1cb9681015635c5e8a14cd07ca0219bc1d?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> Report is 1 commits behind head on develop.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/mysql/pull/345/graphs/tree.svg?width=650&height=150&src=pr&token=ajbbjm3OiO&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/mysql/pull/345?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #345      +/-   ##  
===========================================  
- Coverage    99.04%   99.03%   -0.02%       
===========================================  
  Files          144      144                
  Lines         7148     7148                
===========================================  
- Hits          7080     7079       -1       
- Misses          68       69       +1       
```  
  
| [Files with missing lines](https://app.codecov.io/gh/boostorg/mysql/pull/345?dropdown=coverage&src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/mysql/connection\_pool.hpp](https://app.codecov.io/gh/boostorg/mysql/pull/345?src=pr&el=tree&filepath=include%2Fboost%2Fmysql%2Fconnection_pool.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9teXNxbC9jb25uZWN0aW9uX3Bvb2wuaHBw) | `100.00% <ø> (ø)` | |  
| [.../impl/internal/connection\_pool/connection\_node.hpp](https://app.codecov.io/gh/boostorg/mysql/pull/345?src=pr&el=tree&filepath=include%2Fboost%2Fmysql%2Fimpl%2Finternal%2Fconnection_pool%2Fconnection_node.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9teXNxbC9pbXBsL2ludGVybmFsL2Nvbm5lY3Rpb25fcG9vbC9jb25uZWN0aW9uX25vZGUuaHBw) | `98.70% <100.00%> (-1.30%)` | :arrow_down: |  
| [.../internal/connection\_pool/connection\_pool\_impl.hpp](https://app.codecov.io/gh/boostorg/mysql/pull/345?src=pr&el=tree&filepath=include%2Fboost%2Fmysql%2Fimpl%2Finternal%2Fconnection_pool%2Fconnection_pool_impl.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9teXNxbC9pbXBsL2ludGVybmFsL2Nvbm5lY3Rpb25fcG9vbC9jb25uZWN0aW9uX3Bvb2xfaW1wbC5ocHA=) | `97.90% <100.00%> (ø)` | |  
| [...mysql/impl/internal/connection\_pool/timer\_list.hpp](https://app.codecov.io/gh/boostorg/mysql/pull/345?src=pr&el=tree&filepath=include%2Fboost%2Fmysql%2Fimpl%2Finternal%2Fconnection_pool%2Ftimer_list.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9teXNxbC9pbXBsL2ludGVybmFsL2Nvbm5lY3Rpb25fcG9vbC90aW1lcl9saXN0LmhwcA==) | `100.00% <100.00%> (ø)` | |  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/mysql/pull/345?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/mysql/pull/345?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [1f79e5c...1854de1](https://app.codecov.io/gh/boostorg/mysql/pull/345?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---
