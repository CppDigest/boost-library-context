# #452 Starting operations while engaged in multi-function ops is no longer UB [Merged]

> Username: anarthal  
> Created at: 2025-02-22 14:49:05 UTC  
> Updated at: 2025-02-22 18:15:10 UTC  
> Merged at: 2025-02-22 18:15:03 UTC  
> Closed at: 2025-02-22 18:15:03 UTC  
> Url: https://github.com/boostorg/mysql/pull/452  

Attempting to start an operation involving server communication after a multi-function operation has been started and before all its associated packets have been read now fails with client_errc::engaged_in_multi_function, instead of causing undefined behavior.  
Attempting to start a read_some_rows or read_resultset_head operation with no in-progress multi-function operation now fails with client_errc::not_engaged_in_multi_function.  
Attempting to start an operation that requires an established session before a successful connect now fails with client_errc::not_connected.  
Added the three client_errc enum values mentioned above.  
Renamed detail::connection_status (connection_pool) to node_status.  
Added detail::connection_status (connection_state_data).  
Removed redundant spotcheck tests.  
  
close #448   
close #450

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2025-02-22 15:41:08 UTC  
> Updated_at: 2025-02-22 18:15:10 UTC  
> Url: https://github.com/boostorg/mysql/pull/452#issuecomment-2676267807  

## [Codecov](https://app.codecov.io/gh/boostorg/mysql/pull/452?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
All modified and coverable lines are covered by tests :white_check_mark:  
> Project coverage is 99.02%. Comparing base [(`3868d8a`)](https://app.codecov.io/gh/boostorg/mysql/commit/3868d8a0cfb44c0edb98a725ae8835ae8b4e0077?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) to head [(`1bf98d4`)](https://app.codecov.io/gh/boostorg/mysql/commit/1bf98d4fe52200b67a43460a9079a4c71ab8844a?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> Report is 1 commits behind head on develop.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/mysql/pull/452/graphs/tree.svg?width=650&height=150&src=pr&token=ajbbjm3OiO&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/mysql/pull/452?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #452      +/-   ##  
===========================================  
+ Coverage    98.98%   99.02%   +0.03%       
===========================================  
  Files          142      142                
  Lines         7190     7260      +70       
===========================================  
+ Hits          7117     7189      +72       
+ Misses          73       71       -2       
```  
  
| [Files with missing lines](https://app.codecov.io/gh/boostorg/mysql/pull/452?dropdown=coverage&src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/mysql/client\_errc.hpp](https://app.codecov.io/gh/boostorg/mysql/pull/452?src=pr&el=tree&filepath=include%2Fboost%2Fmysql%2Fclient_errc.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9teXNxbC9jbGllbnRfZXJyYy5ocHA=) | `100.00% <ø> (ø)` | |  
| [include/boost/mysql/detail/connection\_impl.hpp](https://app.codecov.io/gh/boostorg/mysql/pull/452?src=pr&el=tree&filepath=include%2Fboost%2Fmysql%2Fdetail%2Fconnection_impl.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9teXNxbC9kZXRhaWwvY29ubmVjdGlvbl9pbXBsLmhwcA==) | `94.87% <100.00%> (+0.04%)` | :arrow_up: |  
| [include/boost/mysql/impl/connection\_impl.ipp](https://app.codecov.io/gh/boostorg/mysql/pull/452?src=pr&el=tree&filepath=include%2Fboost%2Fmysql%2Fimpl%2Fconnection_impl.ipp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9teXNxbC9pbXBsL2Nvbm5lY3Rpb25faW1wbC5pcHA=) | `100.00% <100.00%> (ø)` | |  
| [include/boost/mysql/impl/error\_categories.ipp](https://app.codecov.io/gh/boostorg/mysql/pull/452?src=pr&el=tree&filepath=include%2Fboost%2Fmysql%2Fimpl%2Ferror_categories.ipp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9teXNxbC9pbXBsL2Vycm9yX2NhdGVnb3JpZXMuaXBw) | `98.75% <100.00%> (+0.10%)` | :arrow_up: |  
| [...nternal/connection\_pool/sansio\_connection\_node.hpp](https://app.codecov.io/gh/boostorg/mysql/pull/452?src=pr&el=tree&filepath=include%2Fboost%2Fmysql%2Fimpl%2Finternal%2Fconnection_pool%2Fsansio_connection_node.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9teXNxbC9pbXBsL2ludGVybmFsL2Nvbm5lY3Rpb25fcG9vbC9zYW5zaW9fY29ubmVjdGlvbl9ub2RlLmhwcA==) | `98.75% <100.00%> (-0.02%)` | :arrow_down: |  
| [...st/mysql/impl/internal/sansio/close\_connection.hpp](https://app.codecov.io/gh/boostorg/mysql/pull/452?src=pr&el=tree&filepath=include%2Fboost%2Fmysql%2Fimpl%2Finternal%2Fsansio%2Fclose_connection.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9teXNxbC9pbXBsL2ludGVybmFsL3NhbnNpby9jbG9zZV9jb25uZWN0aW9uLmhwcA==) | `100.00% <100.00%> (ø)` | |  
| [...clude/boost/mysql/impl/internal/sansio/connect.hpp](https://app.codecov.io/gh/boostorg/mysql/pull/452?src=pr&el=tree&filepath=include%2Fboost%2Fmysql%2Fimpl%2Finternal%2Fsansio%2Fconnect.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9teXNxbC9pbXBsL2ludGVybmFsL3NhbnNpby9jb25uZWN0LmhwcA==) | `100.00% <ø> (ø)` | |  
| [...sql/impl/internal/sansio/connection\_state\_data.hpp](https://app.codecov.io/gh/boostorg/mysql/pull/452?src=pr&el=tree&filepath=include%2Fboost%2Fmysql%2Fimpl%2Finternal%2Fsansio%2Fconnection_state_data.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9teXNxbC9pbXBsL2ludGVybmFsL3NhbnNpby9jb25uZWN0aW9uX3N0YXRlX2RhdGEuaHBw) | `100.00% <100.00%> (ø)` | |  
| [...clude/boost/mysql/impl/internal/sansio/execute.hpp](https://app.codecov.io/gh/boostorg/mysql/pull/452?src=pr&el=tree&filepath=include%2Fboost%2Fmysql%2Fimpl%2Finternal%2Fsansio%2Fexecute.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9teXNxbC9pbXBsL2ludGVybmFsL3NhbnNpby9leGVjdXRlLmhwcA==) | `97.50% <100.00%> (+0.20%)` | :arrow_up: |  
| [...ude/boost/mysql/impl/internal/sansio/handshake.hpp](https://app.codecov.io/gh/boostorg/mysql/pull/452?src=pr&el=tree&filepath=include%2Fboost%2Fmysql%2Fimpl%2Finternal%2Fsansio%2Fhandshake.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9teXNxbC9pbXBsL2ludGVybmFsL3NhbnNpby9oYW5kc2hha2UuaHBw) | `92.03% <100.00%> (ø)` | |  
| ... and [8 more](https://app.codecov.io/gh/boostorg/mysql/pull/452?src=pr&el=tree-more&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | |  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/mysql/pull/452?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/mysql/pull/452?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [3868d8a...1bf98d4](https://app.codecov.io/gh/boostorg/mysql/pull/452?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---
