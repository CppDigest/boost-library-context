# #325 Writes exceeding max buffer size no longer resize the buffer before failing [Merged]

> Username: anarthal  
> Created at: 2024-07-25 15:09:44 UTC  
> Updated at: 2024-07-26 09:46:33 UTC  
> Merged at: 2024-07-26 09:46:32 UTC  
> Closed at: 2024-07-26 09:46:32 UTC  
> Url: https://github.com/boostorg/mysql/pull/325  

close #297

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2024-07-25 15:26:04 UTC  
> Updated_at: 2024-07-26 09:41:13 UTC  
> Url: https://github.com/boostorg/mysql/pull/325#issuecomment-2250675560  

## [Codecov](https://app.codecov.io/gh/boostorg/mysql/pull/325?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
All modified and coverable lines are covered by tests :white_check_mark:  
> Project coverage is 98.51%. Comparing base [(`820e10e`)](https://app.codecov.io/gh/boostorg/mysql/commit/820e10ed55255695cfeb9b110c0888dd77c2b1df?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) to head [(`f0b5c41`)](https://app.codecov.io/gh/boostorg/mysql/commit/f0b5c41ecfa72f5a0fb1dc6f8d7f82228b5afc03?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/mysql/pull/325/graphs/tree.svg?width=650&height=150&src=pr&token=ajbbjm3OiO&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/mysql/pull/325?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #325      +/-   ##  
===========================================  
+ Coverage    98.48%   98.51%   +0.02%       
===========================================  
  Files          138      138                
  Lines         7059     7058       -1       
===========================================  
+ Hits          6952     6953       +1       
+ Misses         107      105       -2       
```  
  
| [Files](https://app.codecov.io/gh/boostorg/mysql/pull/325?dropdown=coverage&src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [...ql/impl/internal/protocol/impl/binary\_protocol.hpp](https://app.codecov.io/gh/boostorg/mysql/pull/325?src=pr&el=tree&filepath=include%2Fboost%2Fmysql%2Fimpl%2Finternal%2Fprotocol%2Fimpl%2Fbinary_protocol.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9teXNxbC9pbXBsL2ludGVybmFsL3Byb3RvY29sL2ltcGwvYmluYXJ5X3Byb3RvY29sLmhwcA==) | `98.91% <100.00%> (ø)` | |  
| [...sql/impl/internal/protocol/impl/protocol\_types.hpp](https://app.codecov.io/gh/boostorg/mysql/pull/325?src=pr&el=tree&filepath=include%2Fboost%2Fmysql%2Fimpl%2Finternal%2Fprotocol%2Fimpl%2Fprotocol_types.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9teXNxbC9pbXBsL2ludGVybmFsL3Byb3RvY29sL2ltcGwvcHJvdG9jb2xfdHlwZXMuaHBw) | `98.91% <100.00%> (-0.11%)` | :arrow_down: |  
| [...l/internal/protocol/impl/serialization\_context.hpp](https://app.codecov.io/gh/boostorg/mysql/pull/325?src=pr&el=tree&filepath=include%2Fboost%2Fmysql%2Fimpl%2Finternal%2Fprotocol%2Fimpl%2Fserialization_context.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9teXNxbC9pbXBsL2ludGVybmFsL3Byb3RvY29sL2ltcGwvc2VyaWFsaXphdGlvbl9jb250ZXh0LmhwcA==) | `100.00% <100.00%> (ø)` | |  
| [...ost/mysql/impl/internal/protocol/serialization.hpp](https://app.codecov.io/gh/boostorg/mysql/pull/325?src=pr&el=tree&filepath=include%2Fboost%2Fmysql%2Fimpl%2Finternal%2Fprotocol%2Fserialization.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9teXNxbC9pbXBsL2ludGVybmFsL3Byb3RvY29sL3NlcmlhbGl6YXRpb24uaHBw) | `98.71% <100.00%> (+0.10%)` | :arrow_up: |  
| [...ost/mysql/impl/internal/sansio/close\_statement.hpp](https://app.codecov.io/gh/boostorg/mysql/pull/325?src=pr&el=tree&filepath=include%2Fboost%2Fmysql%2Fimpl%2Finternal%2Fsansio%2Fclose_statement.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9teXNxbC9pbXBsL2ludGVybmFsL3NhbnNpby9jbG9zZV9zdGF0ZW1lbnQuaHBw) | `100.00% <100.00%> (ø)` | |  
| [...sql/impl/internal/sansio/connection\_state\_data.hpp](https://app.codecov.io/gh/boostorg/mysql/pull/325?src=pr&el=tree&filepath=include%2Fboost%2Fmysql%2Fimpl%2Finternal%2Fsansio%2Fconnection_state_data.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9teXNxbC9pbXBsL2ludGVybmFsL3NhbnNpby9jb25uZWN0aW9uX3N0YXRlX2RhdGEuaHBw) | `100.00% <100.00%> (ø)` | |  
| [include/boost/mysql/impl/internal/sansio/ping.hpp](https://app.codecov.io/gh/boostorg/mysql/pull/325?src=pr&el=tree&filepath=include%2Fboost%2Fmysql%2Fimpl%2Finternal%2Fsansio%2Fping.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9teXNxbC9pbXBsL2ludGVybmFsL3NhbnNpby9waW5nLmhwcA==) | `100.00% <100.00%> (+6.25%)` | :arrow_up: |  
| [...st/mysql/impl/internal/sansio/reset\_connection.hpp](https://app.codecov.io/gh/boostorg/mysql/pull/325?src=pr&el=tree&filepath=include%2Fboost%2Fmysql%2Fimpl%2Finternal%2Fsansio%2Freset_connection.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9teXNxbC9pbXBsL2ludGVybmFsL3NhbnNpby9yZXNldF9jb25uZWN0aW9uLmhwcA==) | `100.00% <100.00%> (+5.00%)` | :arrow_up: |  
| [...oost/mysql/impl/internal/sansio/top\_level\_algo.hpp](https://app.codecov.io/gh/boostorg/mysql/pull/325?src=pr&el=tree&filepath=include%2Fboost%2Fmysql%2Fimpl%2Finternal%2Fsansio%2Ftop_level_algo.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9teXNxbC9pbXBsL2ludGVybmFsL3NhbnNpby90b3BfbGV2ZWxfYWxnby5ocHA=) | `96.42% <ø> (-0.35%)` | :arrow_down: |  
| [include/boost/mysql/impl/pipeline.ipp](https://app.codecov.io/gh/boostorg/mysql/pull/325?src=pr&el=tree&filepath=include%2Fboost%2Fmysql%2Fimpl%2Fpipeline.ipp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9teXNxbC9pbXBsL3BpcGVsaW5lLmlwcA==) | `100.00% <100.00%> (ø)` | |  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/mysql/pull/325?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/mysql/pull/325?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [820e10e...f0b5c41](https://app.codecov.io/gh/boostorg/mysql/pull/325?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---
