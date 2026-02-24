# #291 Removed the pipeline static interface [Merged]

> Username: anarthal  
> Created at: 2024-06-24 16:06:33 UTC  
> Updated at: 2024-06-26 14:47:28 UTC  
> Merged at: 2024-06-26 14:47:27 UTC  
> Closed at: 2024-06-26 14:47:27 UTC  
> Url: https://github.com/boostorg/mysql/pull/291  

close #288

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2024-06-24 16:57:11 UTC  
> Updated_at: 2024-06-26 14:37:20 UTC  
> Url: https://github.com/boostorg/mysql/pull/291#issuecomment-2187014454  

## [Codecov](https://app.codecov.io/gh/boostorg/mysql/pull/291?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
All modified and coverable lines are covered by tests :white_check_mark:  
> Project coverage is 98.53%. Comparing base [(`85819c2`)](https://app.codecov.io/gh/boostorg/mysql/commit/85819c2f36ba6ba1a884a5573d56bcebdfe9a2d9?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) to head [(`227c076`)](https://app.codecov.io/gh/boostorg/mysql/commit/227c076d1d4ad89cf1c45a3919acd8d6738cf56f?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/mysql/pull/291/graphs/tree.svg?width=650&height=150&src=pr&token=ajbbjm3OiO&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/mysql/pull/291?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #291      +/-   ##  
===========================================  
+ Coverage    98.47%   98.53%   +0.05%       
===========================================  
  Files          136      134       -2       
  Lines         6547     6467      -80       
===========================================  
- Hits          6447     6372      -75       
+ Misses         100       95       -5       
```  
  
| [Files](https://app.codecov.io/gh/boostorg/mysql/pull/291?dropdown=coverage&src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/mysql/any\_connection.hpp](https://app.codecov.io/gh/boostorg/mysql/pull/291?src=pr&el=tree&filepath=include%2Fboost%2Fmysql%2Fany_connection.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9teXNxbC9hbnlfY29ubmVjdGlvbi5ocHA=) | `98.88% <100.00%> (ø)` | |  
| [include/boost/mysql/detail/connection\_impl.hpp](https://app.codecov.io/gh/boostorg/mysql/pull/291?src=pr&el=tree&filepath=include%2Fboost%2Fmysql%2Fdetail%2Fconnection_impl.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9teXNxbC9kZXRhaWwvY29ubmVjdGlvbl9pbXBsLmhwcA==) | `95.31% <ø> (-0.08%)` | :arrow_down: |  
| [include/boost/mysql/detail/pipeline.hpp](https://app.codecov.io/gh/boostorg/mysql/pull/291?src=pr&el=tree&filepath=include%2Fboost%2Fmysql%2Fdetail%2Fpipeline.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9teXNxbC9kZXRhaWwvcGlwZWxpbmUuaHBw) | `100.00% <ø> (+3.33%)` | :arrow_up: |  
| [include/boost/mysql/error\_with\_diagnostics.hpp](https://app.codecov.io/gh/boostorg/mysql/pull/291?src=pr&el=tree&filepath=include%2Fboost%2Fmysql%2Ferror_with_diagnostics.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9teXNxbC9lcnJvcl93aXRoX2RpYWdub3N0aWNzLmhwcA==) | `100.00% <ø> (ø)` | |  
| [include/boost/mysql/impl/connection\_impl.ipp](https://app.codecov.io/gh/boostorg/mysql/pull/291?src=pr&el=tree&filepath=include%2Fboost%2Fmysql%2Fimpl%2Fconnection_impl.ipp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9teXNxbC9pbXBsL2Nvbm5lY3Rpb25faW1wbC5pcHA=) | `100.00% <100.00%> (ø)` | |  
| [.../impl/internal/connection\_pool/connection\_node.hpp](https://app.codecov.io/gh/boostorg/mysql/pull/291?src=pr&el=tree&filepath=include%2Fboost%2Fmysql%2Fimpl%2Finternal%2Fconnection_pool%2Fconnection_node.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9teXNxbC9pbXBsL2ludGVybmFsL2Nvbm5lY3Rpb25fcG9vbC9jb25uZWN0aW9uX25vZGUuaHBw) | `100.00% <ø> (ø)` | |  
| [.../internal/connection\_pool/connection\_pool\_impl.hpp](https://app.codecov.io/gh/boostorg/mysql/pull/291?src=pr&el=tree&filepath=include%2Fboost%2Fmysql%2Fimpl%2Finternal%2Fconnection_pool%2Fconnection_pool_impl.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9teXNxbC9pbXBsL2ludGVybmFsL2Nvbm5lY3Rpb25fcG9vbC9jb25uZWN0aW9uX3Bvb2xfaW1wbC5ocHA=) | `98.97% <100.00%> (+0.04%)` | :arrow_up: |  
| [...ost/mysql/impl/internal/sansio/close\_statement.hpp](https://app.codecov.io/gh/boostorg/mysql/pull/291?src=pr&el=tree&filepath=include%2Fboost%2Fmysql%2Fimpl%2Finternal%2Fsansio%2Fclose_statement.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9teXNxbC9pbXBsL2ludGVybmFsL3NhbnNpby9jbG9zZV9zdGF0ZW1lbnQuaHBw) | `100.00% <100.00%> (ø)` | |  
| [include/boost/mysql/impl/internal/sansio/ping.hpp](https://app.codecov.io/gh/boostorg/mysql/pull/291?src=pr&el=tree&filepath=include%2Fboost%2Fmysql%2Fimpl%2Finternal%2Fsansio%2Fping.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9teXNxbC9pbXBsL2ludGVybmFsL3NhbnNpby9waW5nLmhwcA==) | `93.75% <100.00%> (ø)` | |  
| [...st/mysql/impl/internal/sansio/reset\_connection.hpp](https://app.codecov.io/gh/boostorg/mysql/pull/291?src=pr&el=tree&filepath=include%2Fboost%2Fmysql%2Fimpl%2Finternal%2Fsansio%2Freset_connection.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9teXNxbC9pbXBsL2ludGVybmFsL3NhbnNpby9yZXNldF9jb25uZWN0aW9uLmhwcA==) | `95.00% <100.00%> (ø)` | |  
| ... and [3 more](https://app.codecov.io/gh/boostorg/mysql/pull/291?src=pr&el=tree-more&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | |  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/mysql/pull/291?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/mysql/pull/291?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [85819c2...227c076](https://app.codecov.io/gh/boostorg/mysql/pull/291?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---
