# #342 connection_pool thread-safety is now more robust [Merged]

> Username: anarthal  
> Created at: 2024-09-06 17:41:46 UTC  
> Updated at: 2024-09-14 11:07:01 UTC  
> Merged at: 2024-09-14 11:06:54 UTC  
> Closed at: 2024-09-14 11:06:54 UTC  
> Url: https://github.com/boostorg/mysql/pull/342  

Thread-safety is now achieved using pool_params::thread_safe. Removed pool_executor_params::thread_safe  
Unsafe pools now use the standard Asio semantics  
Safe pools are now safe even if the handlers' associated executors point to a context different from the pool's context  
  
close #268

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2024-09-09 08:50:42 UTC  
> Updated_at: 2024-09-14 11:07:01 UTC  
> Url: https://github.com/boostorg/mysql/pull/342#issuecomment-2337517541  

## [Codecov](https://app.codecov.io/gh/boostorg/mysql/pull/342?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
Attention: Patch coverage is `98.92473%` with `1 line` in your changes missing coverage. Please review.  
> Project coverage is 99.04%. Comparing base [(`4a1604d`)](https://app.codecov.io/gh/boostorg/mysql/commit/4a1604d685b7e2e2c8ad9cede326f712a17fb69b?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) to head [(`fe933f1`)](https://app.codecov.io/gh/boostorg/mysql/commit/fe933f14024e8d734160dc9e7bec3ee663732292?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> Report is 1 commits behind head on develop.  
  
| [Files with missing lines](https://app.codecov.io/gh/boostorg/mysql/pull/342?dropdown=coverage&src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Patch % | Lines |  
|---|---|---|  
| [.../internal/connection\_pool/connection\_pool\_impl.hpp](https://app.codecov.io/gh/boostorg/mysql/pull/342?src=pr&el=tree&filepath=include%2Fboost%2Fmysql%2Fimpl%2Finternal%2Fconnection_pool%2Fconnection_pool_impl.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9teXNxbC9pbXBsL2ludGVybmFsL2Nvbm5lY3Rpb25fcG9vbC9jb25uZWN0aW9uX3Bvb2xfaW1wbC5ocHA=) | 98.68% | [1 Missing :warning: ](https://app.codecov.io/gh/boostorg/mysql/pull/342?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) |  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/mysql/pull/342/graphs/tree.svg?width=650&height=150&src=pr&token=ajbbjm3OiO&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/mysql/pull/342?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #342      +/-   ##  
===========================================  
+ Coverage    99.03%   99.04%   +0.01%       
===========================================  
  Files          144      144                
  Lines         7122     7148      +26       
===========================================  
+ Hits          7053     7080      +27       
+ Misses          69       68       -1       
```  
  
| [Files with missing lines](https://app.codecov.io/gh/boostorg/mysql/pull/342?dropdown=coverage&src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/mysql/connection\_pool.hpp](https://app.codecov.io/gh/boostorg/mysql/pull/342?src=pr&el=tree&filepath=include%2Fboost%2Fmysql%2Fconnection_pool.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9teXNxbC9jb25uZWN0aW9uX3Bvb2wuaHBw) | `100.00% <100.00%> (ø)` | |  
| [include/boost/mysql/impl/connection\_pool.ipp](https://app.codecov.io/gh/boostorg/mysql/pull/342?src=pr&el=tree&filepath=include%2Fboost%2Fmysql%2Fimpl%2Fconnection_pool.ipp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9teXNxbC9pbXBsL2Nvbm5lY3Rpb25fcG9vbC5pcHA=) | `100.00% <100.00%> (+7.69%)` | :arrow_up: |  
| [.../impl/internal/connection\_pool/connection\_node.hpp](https://app.codecov.io/gh/boostorg/mysql/pull/342?src=pr&el=tree&filepath=include%2Fboost%2Fmysql%2Fimpl%2Finternal%2Fconnection_pool%2Fconnection_node.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9teXNxbC9pbXBsL2ludGVybmFsL2Nvbm5lY3Rpb25fcG9vbC9jb25uZWN0aW9uX25vZGUuaHBw) | `100.00% <100.00%> (ø)` | |  
| [.../internal/connection\_pool/internal\_pool\_params.hpp](https://app.codecov.io/gh/boostorg/mysql/pull/342?src=pr&el=tree&filepath=include%2Fboost%2Fmysql%2Fimpl%2Finternal%2Fconnection_pool%2Finternal_pool_params.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9teXNxbC9pbXBsL2ludGVybmFsL2Nvbm5lY3Rpb25fcG9vbC9pbnRlcm5hbF9wb29sX3BhcmFtcy5ocHA=) | `100.00% <100.00%> (ø)` | |  
| [include/boost/mysql/pool\_params.hpp](https://app.codecov.io/gh/boostorg/mysql/pull/342?src=pr&el=tree&filepath=include%2Fboost%2Fmysql%2Fpool_params.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9teXNxbC9wb29sX3BhcmFtcy5ocHA=) | `100.00% <ø> (ø)` | |  
| [.../internal/connection\_pool/connection\_pool\_impl.hpp](https://app.codecov.io/gh/boostorg/mysql/pull/342?src=pr&el=tree&filepath=include%2Fboost%2Fmysql%2Fimpl%2Finternal%2Fconnection_pool%2Fconnection_pool_impl.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9teXNxbC9pbXBsL2ludGVybmFsL2Nvbm5lY3Rpb25fcG9vbC9jb25uZWN0aW9uX3Bvb2xfaW1wbC5ocHA=) | `97.90% <98.68%> (-0.18%)` | :arrow_down: |  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/mysql/pull/342?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/mysql/pull/342?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [4a1604d...fe933f1](https://app.codecov.io/gh/boostorg/mysql/pull/342?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---
