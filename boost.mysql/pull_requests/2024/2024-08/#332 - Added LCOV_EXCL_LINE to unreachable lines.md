# #332 Added LCOV_EXCL_LINE to unreachable lines [Merged]

> Username: anarthal  
> Created at: 2024-08-10 16:07:17 UTC  
> Updated at: 2024-08-13 08:49:40 UTC  
> Merged at: 2024-08-13 08:49:38 UTC  
> Closed at: 2024-08-13 08:49:38 UTC  
> Url: https://github.com/boostorg/mysql/pull/332  

close #227

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2024-08-10 17:23:21 UTC  
> Updated_at: 2024-08-12 10:09:39 UTC  
> Url: https://github.com/boostorg/mysql/pull/332#issuecomment-2282217605  

## [Codecov](https://app.codecov.io/gh/boostorg/mysql/pull/332?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
All modified and coverable lines are covered by tests :white_check_mark:  
> Project coverage is 99.01%. Comparing base [(`36dc476`)](https://app.codecov.io/gh/boostorg/mysql/commit/36dc47693eb9894889fb3b7ca0e5079feed7c573?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) to head [(`ebeaade`)](https://app.codecov.io/gh/boostorg/mysql/commit/ebeaadeeebbda6a2c5b15d582f69323de940e786?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/mysql/pull/332/graphs/tree.svg?width=650&height=150&src=pr&token=ajbbjm3OiO&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/mysql/pull/332?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #332      +/-   ##  
===========================================  
+ Coverage    98.69%   99.01%   +0.31%       
===========================================  
  Files          141      141                
  Lines         7115     7087      -28       
===========================================  
- Hits          7022     7017       -5       
+ Misses          93       70      -23       
```  
  
| [Files](https://app.codecov.io/gh/boostorg/mysql/pull/332?dropdown=coverage&src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [...clude/boost/mysql/detail/engine\_stream\_adaptor.hpp](https://app.codecov.io/gh/boostorg/mysql/pull/332?src=pr&el=tree&filepath=include%2Fboost%2Fmysql%2Fdetail%2Fengine_stream_adaptor.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9teXNxbC9kZXRhaWwvZW5naW5lX3N0cmVhbV9hZGFwdG9yLmhwcA==) | `100.00% <ø> (+13.54%)` | :arrow_up: |  
| [include/boost/mysql/impl/field\_view.hpp](https://app.codecov.io/gh/boostorg/mysql/pull/332?src=pr&el=tree&filepath=include%2Fboost%2Fmysql%2Fimpl%2Ffield_view.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9teXNxbC9pbXBsL2ZpZWxkX3ZpZXcuaHBw) | `97.97% <ø> (+0.97%)` | :arrow_up: |  
| [include/boost/mysql/impl/field\_view.ipp](https://app.codecov.io/gh/boostorg/mysql/pull/332?src=pr&el=tree&filepath=include%2Fboost%2Fmysql%2Fimpl%2Ffield_view.ipp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9teXNxbC9pbXBsL2ZpZWxkX3ZpZXcuaXBw) | `96.77% <ø> (+3.02%)` | :arrow_up: |  
| [include/boost/mysql/impl/format\_sql.ipp](https://app.codecov.io/gh/boostorg/mysql/pull/332?src=pr&el=tree&filepath=include%2Fboost%2Fmysql%2Fimpl%2Fformat_sql.ipp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9teXNxbC9pbXBsL2Zvcm1hdF9zcWwuaXBw) | `100.00% <ø> (+0.83%)` | :arrow_up: |  
| [.../impl/internal/connection\_pool/connection\_node.hpp](https://app.codecov.io/gh/boostorg/mysql/pull/332?src=pr&el=tree&filepath=include%2Fboost%2Fmysql%2Fimpl%2Finternal%2Fconnection_pool%2Fconnection_node.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9teXNxbC9pbXBsL2ludGVybmFsL2Nvbm5lY3Rpb25fcG9vbC9jb25uZWN0aW9uX25vZGUuaHBw) | `100.00% <ø> (ø)` | |  
| [...ql/impl/internal/protocol/impl/binary\_protocol.hpp](https://app.codecov.io/gh/boostorg/mysql/pull/332?src=pr&el=tree&filepath=include%2Fboost%2Fmysql%2Fimpl%2Finternal%2Fprotocol%2Fimpl%2Fbinary_protocol.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9teXNxbC9pbXBsL2ludGVybmFsL3Byb3RvY29sL2ltcGwvYmluYXJ5X3Byb3RvY29sLmhwcA==) | `99.45% <ø> (+0.54%)` | :arrow_up: |  
| [...internal/protocol/impl/deserialization\_context.hpp](https://app.codecov.io/gh/boostorg/mysql/pull/332?src=pr&el=tree&filepath=include%2Fboost%2Fmysql%2Fimpl%2Finternal%2Fprotocol%2Fimpl%2Fdeserialization_context.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9teXNxbC9pbXBsL2ludGVybmFsL3Byb3RvY29sL2ltcGwvZGVzZXJpYWxpemF0aW9uX2NvbnRleHQuaHBw) | `100.00% <ø> (+3.44%)` | :arrow_up: |  
| [...ost/mysql/impl/internal/protocol/serialization.hpp](https://app.codecov.io/gh/boostorg/mysql/pull/332?src=pr&el=tree&filepath=include%2Fboost%2Fmysql%2Fimpl%2Finternal%2Fprotocol%2Fserialization.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9teXNxbC9pbXBsL2ludGVybmFsL3Byb3RvY29sL3NlcmlhbGl6YXRpb24uaHBw) | `100.00% <ø> (+1.28%)` | :arrow_up: |  
| [.../boost/mysql/impl/internal/sansio/run\_pipeline.hpp](https://app.codecov.io/gh/boostorg/mysql/pull/332?src=pr&el=tree&filepath=include%2Fboost%2Fmysql%2Fimpl%2Finternal%2Fsansio%2Frun_pipeline.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9teXNxbC9pbXBsL2ludGVybmFsL3NhbnNpby9ydW5fcGlwZWxpbmUuaHBw) | `100.00% <ø> (+1.06%)` | :arrow_up: |  
| [...ost/mysql/impl/internal/sansio/start\_execution.hpp](https://app.codecov.io/gh/boostorg/mysql/pull/332?src=pr&el=tree&filepath=include%2Fboost%2Fmysql%2Fimpl%2Finternal%2Fsansio%2Fstart_execution.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9teXNxbC9pbXBsL2ludGVybmFsL3NhbnNpby9zdGFydF9leGVjdXRpb24uaHBw) | `97.77% <ø> (+4.16%)` | :arrow_up: |  
| ... and [1 more](https://app.codecov.io/gh/boostorg/mysql/pull/332?src=pr&el=tree-more&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | |  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/mysql/pull/332?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/mysql/pull/332?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [36dc476...ebeaade](https://app.codecov.io/gh/boostorg/mysql/pull/332?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---
