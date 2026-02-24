# #294 Added a max limit to any_connection buffer size [Merged]

> Username: anarthal  
> Created at: 2024-06-26 17:02:35 UTC  
> Updated at: 2024-06-30 13:49:29 UTC  
> Merged at: 2024-06-30 13:49:27 UTC  
> Closed at: 2024-06-30 13:49:27 UTC  
> Url: https://github.com/boostorg/mysql/pull/294  

any_connection max buffer size is now 64MB  
  
close #278

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2024-06-27 21:18:07 UTC  
> Updated_at: 2024-06-30 13:39:30 UTC  
> Url: https://github.com/boostorg/mysql/pull/294#issuecomment-2195680638  

## [Codecov](https://app.codecov.io/gh/boostorg/mysql/pull/294?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
All modified and coverable lines are covered by tests :white_check_mark:  
> Project coverage is 98.53%. Comparing base [(`d1f1219`)](https://app.codecov.io/gh/boostorg/mysql/commit/d1f1219e4b1766e2df802a81e8181d9e2167ccf4?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) to head [(`a5d488d`)](https://app.codecov.io/gh/boostorg/mysql/commit/a5d488d2700c0b3f117469988690eacfc3502c86?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/mysql/pull/294/graphs/tree.svg?width=650&height=150&src=pr&token=ajbbjm3OiO&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/mysql/pull/294?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@           Coverage Diff            @@  
##           develop     #294   +/-   ##  
========================================  
  Coverage    98.53%   98.53%             
========================================  
  Files          134      134             
  Lines         6467     6480   +13       
========================================  
+ Hits          6372     6385   +13       
  Misses          95       95             
```  
  
| [Files](https://app.codecov.io/gh/boostorg/mysql/pull/294?dropdown=coverage&src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/mysql/any\_connection.hpp](https://app.codecov.io/gh/boostorg/mysql/pull/294?src=pr&el=tree&filepath=include%2Fboost%2Fmysql%2Fany_connection.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9teXNxbC9hbnlfY29ubmVjdGlvbi5ocHA=) | `98.89% <100.00%> (+0.01%)` | :arrow_up: |  
| [include/boost/mysql/connection.hpp](https://app.codecov.io/gh/boostorg/mysql/pull/294?src=pr&el=tree&filepath=include%2Fboost%2Fmysql%2Fconnection.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9teXNxbC9jb25uZWN0aW9uLmhwcA==) | `100.00% <100.00%> (ø)` | |  
| [include/boost/mysql/detail/connection\_impl.hpp](https://app.codecov.io/gh/boostorg/mysql/pull/294?src=pr&el=tree&filepath=include%2Fboost%2Fmysql%2Fdetail%2Fconnection_impl.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9teXNxbC9kZXRhaWwvY29ubmVjdGlvbl9pbXBsLmhwcA==) | `95.31% <ø> (ø)` | |  
| [include/boost/mysql/impl/connection\_impl.ipp](https://app.codecov.io/gh/boostorg/mysql/pull/294?src=pr&el=tree&filepath=include%2Fboost%2Fmysql%2Fimpl%2Fconnection_impl.ipp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9teXNxbC9pbXBsL2Nvbm5lY3Rpb25faW1wbC5pcHA=) | `100.00% <100.00%> (ø)` | |  
| [include/boost/mysql/impl/error\_categories.ipp](https://app.codecov.io/gh/boostorg/mysql/pull/294?src=pr&el=tree&filepath=include%2Fboost%2Fmysql%2Fimpl%2Ferror_categories.ipp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9teXNxbC9pbXBsL2Vycm9yX2NhdGVnb3JpZXMuaXBw) | `97.14% <100.00%> (+0.08%)` | :arrow_up: |  
| [.../internal/connection\_pool/internal\_pool\_params.hpp](https://app.codecov.io/gh/boostorg/mysql/pull/294?src=pr&el=tree&filepath=include%2Fboost%2Fmysql%2Fimpl%2Finternal%2Fconnection_pool%2Finternal_pool_params.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9teXNxbC9pbXBsL2ludGVybmFsL2Nvbm5lY3Rpb25fcG9vbC9pbnRlcm5hbF9wb29sX3BhcmFtcy5ocHA=) | `100.00% <100.00%> (ø)` | |  
| [...st/mysql/impl/internal/sansio/connection\_state.hpp](https://app.codecov.io/gh/boostorg/mysql/pull/294?src=pr&el=tree&filepath=include%2Fboost%2Fmysql%2Fimpl%2Finternal%2Fsansio%2Fconnection_state.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9teXNxbC9pbXBsL2ludGVybmFsL3NhbnNpby9jb25uZWN0aW9uX3N0YXRlLmhwcA==) | `100.00% <100.00%> (ø)` | |  
| [...sql/impl/internal/sansio/connection\_state\_data.hpp](https://app.codecov.io/gh/boostorg/mysql/pull/294?src=pr&el=tree&filepath=include%2Fboost%2Fmysql%2Fimpl%2Finternal%2Fsansio%2Fconnection_state_data.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9teXNxbC9pbXBsL2ludGVybmFsL3NhbnNpby9jb25uZWN0aW9uX3N0YXRlX2RhdGEuaHBw) | `100.00% <100.00%> (ø)` | |  
| [...oost/mysql/impl/internal/sansio/message\_reader.hpp](https://app.codecov.io/gh/boostorg/mysql/pull/294?src=pr&el=tree&filepath=include%2Fboost%2Fmysql%2Fimpl%2Finternal%2Fsansio%2Fmessage_reader.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9teXNxbC9pbXBsL2ludGVybmFsL3NhbnNpby9tZXNzYWdlX3JlYWRlci5ocHA=) | `100.00% <100.00%> (ø)` | |  
| [...e/boost/mysql/impl/internal/sansio/read\_buffer.hpp](https://app.codecov.io/gh/boostorg/mysql/pull/294?src=pr&el=tree&filepath=include%2Fboost%2Fmysql%2Fimpl%2Finternal%2Fsansio%2Fread_buffer.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9teXNxbC9pbXBsL2ludGVybmFsL3NhbnNpby9yZWFkX2J1ZmZlci5ocHA=) | `100.00% <100.00%> (ø)` | |  
| ... and [3 more](https://app.codecov.io/gh/boostorg/mysql/pull/294?src=pr&el=tree-more&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | |  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/mysql/pull/294?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/mysql/pull/294?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [d1f1219...a5d488d](https://app.codecov.io/gh/boostorg/mysql/pull/294?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---
