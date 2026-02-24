# #446 Added quit_connection unit tests and refactored [Merged]

> Username: anarthal  
> Created at: 2025-02-17 16:43:09 UTC  
> Updated at: 2025-02-17 17:46:54 UTC  
> Merged at: 2025-02-17 17:46:46 UTC  
> Closed at: 2025-02-17 17:46:46 UTC  
> Url: https://github.com/boostorg/mysql/pull/446  

Quit now correctly marks TLS as terminated, even if it encounters an error  
Removed ssl_state

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2025-02-17 17:25:41 UTC  
> Updated_at: 2025-02-17 17:46:54 UTC  
> Url: https://github.com/boostorg/mysql/pull/446#issuecomment-2663724182  

## [Codecov](https://app.codecov.io/gh/boostorg/mysql/pull/446?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
All modified and coverable lines are covered by tests :white_check_mark:  
> Project coverage is 98.97%. Comparing base [(`07200f1`)](https://app.codecov.io/gh/boostorg/mysql/commit/07200f17c28293e910151abbc46d22eeff944384?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) to head [(`512cb53`)](https://app.codecov.io/gh/boostorg/mysql/commit/512cb53896b3dd4b4058b8348bbe2acb00f6697a?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> Report is 1 commits behind head on develop.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/mysql/pull/446/graphs/tree.svg?width=650&height=150&src=pr&token=ajbbjm3OiO&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/mysql/pull/446?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #446      +/-   ##  
===========================================  
+ Coverage    98.95%   98.97%   +0.01%       
===========================================  
  Files          142      142                
  Lines         7192     7189       -3       
===========================================  
- Hits          7117     7115       -2       
+ Misses          75       74       -1       
```  
  
| [Files with missing lines](https://app.codecov.io/gh/boostorg/mysql/pull/446?dropdown=coverage&src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/mysql/impl/connection\_impl.ipp](https://app.codecov.io/gh/boostorg/mysql/pull/446?src=pr&el=tree&filepath=include%2Fboost%2Fmysql%2Fimpl%2Fconnection_impl.ipp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9teXNxbC9pbXBsL2Nvbm5lY3Rpb25faW1wbC5pcHA=) | `100.00% <100.00%> (ø)` | |  
| [...sql/impl/internal/sansio/connection\_state\_data.hpp](https://app.codecov.io/gh/boostorg/mysql/pull/446?src=pr&el=tree&filepath=include%2Fboost%2Fmysql%2Fimpl%2Finternal%2Fsansio%2Fconnection_state_data.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9teXNxbC9pbXBsL2ludGVybmFsL3NhbnNpby9jb25uZWN0aW9uX3N0YXRlX2RhdGEuaHBw) | `100.00% <100.00%> (ø)` | |  
| [...ude/boost/mysql/impl/internal/sansio/handshake.hpp](https://app.codecov.io/gh/boostorg/mysql/pull/446?src=pr&el=tree&filepath=include%2Fboost%2Fmysql%2Fimpl%2Finternal%2Fsansio%2Fhandshake.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9teXNxbC9pbXBsL2ludGVybmFsL3NhbnNpby9oYW5kc2hha2UuaHBw) | `92.03% <100.00%> (ø)` | |  
| [...ost/mysql/impl/internal/sansio/quit\_connection.hpp](https://app.codecov.io/gh/boostorg/mysql/pull/446?src=pr&el=tree&filepath=include%2Fboost%2Fmysql%2Fimpl%2Finternal%2Fsansio%2Fquit_connection.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9teXNxbC9pbXBsL2ludGVybmFsL3NhbnNpby9xdWl0X2Nvbm5lY3Rpb24uaHBw) | `100.00% <100.00%> (+8.33%)` | :arrow_up: |  
| [...oost/mysql/impl/internal/sansio/top\_level\_algo.hpp](https://app.codecov.io/gh/boostorg/mysql/pull/446?src=pr&el=tree&filepath=include%2Fboost%2Fmysql%2Fimpl%2Finternal%2Fsansio%2Ftop_level_algo.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9teXNxbC9pbXBsL2ludGVybmFsL3NhbnNpby90b3BfbGV2ZWxfYWxnby5ocHA=) | `96.77% <ø> (ø)` | |  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/mysql/pull/446?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/mysql/pull/446?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [07200f1...512cb53](https://app.codecov.io/gh/boostorg/mysql/pull/446?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---
