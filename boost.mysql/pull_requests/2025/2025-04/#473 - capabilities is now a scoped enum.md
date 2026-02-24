# #473 capabilities is now a scoped enum [Merged]

> Username: anarthal  
> Created at: 2025-04-25 16:07:55 UTC  
> Updated at: 2025-04-25 18:12:01 UTC  
> Merged at: 2025-04-25 18:11:19 UTC  
> Closed at: 2025-04-25 18:11:19 UTC  
> Url: https://github.com/boostorg/mysql/pull/473  

close #471

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2025-04-25 16:53:49 UTC  
> Updated_at: 2025-04-25 18:12:00 UTC  
> Url: https://github.com/boostorg/mysql/pull/473#issuecomment-2830933546  

## [Codecov](https://app.codecov.io/gh/boostorg/mysql/pull/473?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
All modified and coverable lines are covered by tests :white_check_mark:  
> Project coverage is 99.14%. Comparing base [(`cbe9270`)](https://app.codecov.io/gh/boostorg/mysql/commit/cbe9270253a09356ff6f7ba071991d6a35d35daa?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) to head [(`c07603f`)](https://app.codecov.io/gh/boostorg/mysql/commit/c07603fc3688af7e047f4205e1752fb231e75040?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> Report is 1 commits behind head on develop.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/mysql/pull/473/graphs/tree.svg?width=650&height=150&src=pr&token=ajbbjm3OiO&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/mysql/pull/473?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #473      +/-   ##  
===========================================  
- Coverage    99.14%   99.14%   -0.01%       
===========================================  
  Files          142      142                
  Lines         7280     7278       -2       
===========================================  
- Hits          7218     7216       -2       
  Misses          62       62                
```  
  
| [Files with missing lines](https://app.codecov.io/gh/boostorg/mysql/pull/473?dropdown=coverage&src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [...oost/mysql/impl/internal/protocol/capabilities.hpp](https://app.codecov.io/gh/boostorg/mysql/pull/473?src=pr&el=tree&filepath=include%2Fboost%2Fmysql%2Fimpl%2Finternal%2Fprotocol%2Fcapabilities.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9teXNxbC9pbXBsL2ludGVybmFsL3Byb3RvY29sL2NhcGFiaWxpdGllcy5ocHA=) | `100.00% <100.00%> (ø)` | |  
| [...t/mysql/impl/internal/protocol/deserialization.hpp](https://app.codecov.io/gh/boostorg/mysql/pull/473?src=pr&el=tree&filepath=include%2Fboost%2Fmysql%2Fimpl%2Finternal%2Fprotocol%2Fdeserialization.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9teXNxbC9pbXBsL2ludGVybmFsL3Byb3RvY29sL2Rlc2VyaWFsaXphdGlvbi5ocHA=) | `97.69% <100.00%> (ø)` | |  
| [...ost/mysql/impl/internal/protocol/serialization.hpp](https://app.codecov.io/gh/boostorg/mysql/pull/473?src=pr&el=tree&filepath=include%2Fboost%2Fmysql%2Fimpl%2Finternal%2Fprotocol%2Fserialization.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9teXNxbC9pbXBsL2ludGVybmFsL3Byb3RvY29sL3NlcmlhbGl6YXRpb24uaHBw) | `100.00% <100.00%> (ø)` | |  
| [...sql/impl/internal/sansio/connection\_state\_data.hpp](https://app.codecov.io/gh/boostorg/mysql/pull/473?src=pr&el=tree&filepath=include%2Fboost%2Fmysql%2Fimpl%2Finternal%2Fsansio%2Fconnection_state_data.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9teXNxbC9pbXBsL2ludGVybmFsL3NhbnNpby9jb25uZWN0aW9uX3N0YXRlX2RhdGEuaHBw) | `100.00% <100.00%> (ø)` | |  
| [...ude/boost/mysql/impl/internal/sansio/handshake.hpp](https://app.codecov.io/gh/boostorg/mysql/pull/473?src=pr&el=tree&filepath=include%2Fboost%2Fmysql%2Fimpl%2Finternal%2Fsansio%2Fhandshake.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9teXNxbC9pbXBsL2ludGVybmFsL3NhbnNpby9oYW5kc2hha2UuaHBw) | `98.27% <100.00%> (+0.04%)` | :arrow_up: |  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/mysql/pull/473?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/mysql/pull/473?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [cbe9270...c07603f](https://app.codecov.io/gh/boostorg/mysql/pull/473?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>  
<details><summary> :rocket: New features to boost your workflow: </summary>  
  
- :snowflake: [Test Analytics](https://docs.codecov.com/docs/test-analytics): Detect flaky tests, report on failures, and find test suite problems.  
</details>

---
