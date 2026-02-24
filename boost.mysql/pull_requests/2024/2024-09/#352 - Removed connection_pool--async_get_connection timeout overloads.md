# #352 Removed connection_pool::async_get_connection timeout overloads [Merged]

> Username: anarthal  
> Created at: 2024-09-23 16:25:36 UTC  
> Updated at: 2024-09-26 06:30:53 UTC  
> Merged at: 2024-09-26 06:30:52 UTC  
> Closed at: 2024-09-26 06:30:52 UTC  
> Url: https://github.com/boostorg/mysql/pull/352  

Please use asio::cancel_after, instead.  
async_get_connection now uniformly fails with asio::error::operation_aborted when cancelled  
Last connection error code information is now included in async_get_connection output diagnostics  
Removed client_errc::timeout, client_errc::cancelled  
  
close #349

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2024-09-24 19:22:01 UTC  
> Updated_at: 2024-09-24 19:55:54 UTC  
> Url: https://github.com/boostorg/mysql/pull/352#issuecomment-2372157250  

## [Codecov](https://app.codecov.io/gh/boostorg/mysql/pull/352?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
Attention: Patch coverage is `99.14530%` with `1 line` in your changes missing coverage. Please review.  
> Project coverage is 99.05%. Comparing base [(`a84b774`)](https://app.codecov.io/gh/boostorg/mysql/commit/a84b774befc7b3380ddae12f2387301cbd44895e?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) to head [(`3ae8803`)](https://app.codecov.io/gh/boostorg/mysql/commit/3ae880307e98a5320110e585c38d4f52c11c6464?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
| [Files with missing lines](https://app.codecov.io/gh/boostorg/mysql/pull/352?dropdown=coverage&src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Patch % | Lines |  
|---|---|---|  
| [...nternal/connection\_pool/sansio\_connection\_node.hpp](https://app.codecov.io/gh/boostorg/mysql/pull/352?src=pr&el=tree&filepath=include%2Fboost%2Fmysql%2Fimpl%2Finternal%2Fconnection_pool%2Fsansio_connection_node.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9teXNxbC9pbXBsL2ludGVybmFsL2Nvbm5lY3Rpb25fcG9vbC9zYW5zaW9fY29ubmVjdGlvbl9ub2RlLmhwcA==) | 95.00% | [1 Missing :warning: ](https://app.codecov.io/gh/boostorg/mysql/pull/352?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) |  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/mysql/pull/352/graphs/tree.svg?width=650&height=150&src=pr&token=ajbbjm3OiO&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/mysql/pull/352?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #352      +/-   ##  
===========================================  
+ Coverage    99.02%   99.05%   +0.02%       
===========================================  
  Files          144      143       -1       
  Lines         7192     7185       -7       
===========================================  
- Hits          7122     7117       -5       
+ Misses          70       68       -2       
```  
  
| [Files with missing lines](https://app.codecov.io/gh/boostorg/mysql/pull/352?dropdown=coverage&src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/mysql/client\_errc.hpp](https://app.codecov.io/gh/boostorg/mysql/pull/352?src=pr&el=tree&filepath=include%2Fboost%2Fmysql%2Fclient_errc.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9teXNxbC9jbGllbnRfZXJyYy5ocHA=) | `100.00% <ø> (ø)` | |  
| [include/boost/mysql/connection\_pool.hpp](https://app.codecov.io/gh/boostorg/mysql/pull/352?src=pr&el=tree&filepath=include%2Fboost%2Fmysql%2Fconnection_pool.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9teXNxbC9jb25uZWN0aW9uX3Bvb2wuaHBw) | `100.00% <100.00%> (ø)` | |  
| [include/boost/mysql/diagnostics.hpp](https://app.codecov.io/gh/boostorg/mysql/pull/352?src=pr&el=tree&filepath=include%2Fboost%2Fmysql%2Fdiagnostics.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9teXNxbC9kaWFnbm9zdGljcy5ocHA=) | `100.00% <ø> (ø)` | |  
| [include/boost/mysql/impl/connection\_pool.ipp](https://app.codecov.io/gh/boostorg/mysql/pull/352?src=pr&el=tree&filepath=include%2Fboost%2Fmysql%2Fimpl%2Fconnection_pool.ipp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9teXNxbC9pbXBsL2Nvbm5lY3Rpb25fcG9vbC5pcHA=) | `100.00% <100.00%> (ø)` | |  
| [include/boost/mysql/impl/error\_categories.ipp](https://app.codecov.io/gh/boostorg/mysql/pull/352?src=pr&el=tree&filepath=include%2Fboost%2Fmysql%2Fimpl%2Ferror_categories.ipp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9teXNxbC9pbXBsL2Vycm9yX2NhdGVnb3JpZXMuaXBw) | `98.61% <100.00%> (+1.46%)` | :arrow_up: |  
| [.../impl/internal/connection\_pool/connection\_node.hpp](https://app.codecov.io/gh/boostorg/mysql/pull/352?src=pr&el=tree&filepath=include%2Fboost%2Fmysql%2Fimpl%2Finternal%2Fconnection_pool%2Fconnection_node.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9teXNxbC9pbXBsL2ludGVybmFsL2Nvbm5lY3Rpb25fcG9vbC9jb25uZWN0aW9uX25vZGUuaHBw) | `98.73% <100.00%> (+0.03%)` | :arrow_up: |  
| [.../internal/connection\_pool/connection\_pool\_impl.hpp](https://app.codecov.io/gh/boostorg/mysql/pull/352?src=pr&el=tree&filepath=include%2Fboost%2Fmysql%2Fimpl%2Finternal%2Fconnection_pool%2Fconnection_pool_impl.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9teXNxbC9pbXBsL2ludGVybmFsL2Nvbm5lY3Rpb25fcG9vbC9jb25uZWN0aW9uX3Bvb2xfaW1wbC5ocHA=) | `98.87% <100.00%> (+0.97%)` | :arrow_up: |  
| [...impl/internal/connection\_pool/run\_with\_timeout.hpp](https://app.codecov.io/gh/boostorg/mysql/pull/352?src=pr&el=tree&filepath=include%2Fboost%2Fmysql%2Fimpl%2Finternal%2Fconnection_pool%2Frun_with_timeout.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9teXNxbC9pbXBsL2ludGVybmFsL2Nvbm5lY3Rpb25fcG9vbC9ydW5fd2l0aF90aW1lb3V0LmhwcA==) | `100.00% <100.00%> (ø)` | |  
| [include/boost/mysql/impl/is\_fatal\_error.ipp](https://app.codecov.io/gh/boostorg/mysql/pull/352?src=pr&el=tree&filepath=include%2Fboost%2Fmysql%2Fimpl%2Fis_fatal_error.ipp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9teXNxbC9pbXBsL2lzX2ZhdGFsX2Vycm9yLmlwcA==) | `100.00% <ø> (ø)` | |  
| [...nternal/connection\_pool/sansio\_connection\_node.hpp](https://app.codecov.io/gh/boostorg/mysql/pull/352?src=pr&el=tree&filepath=include%2Fboost%2Fmysql%2Fimpl%2Finternal%2Fconnection_pool%2Fsansio_connection_node.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9teXNxbC9pbXBsL2ludGVybmFsL2Nvbm5lY3Rpb25fcG9vbC9zYW5zaW9fY29ubmVjdGlvbl9ub2RlLmhwcA==) | `98.64% <95.00%> (-1.36%)` | :arrow_down: |  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/mysql/pull/352?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/mysql/pull/352?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [a84b774...3ae8803](https://app.codecov.io/gh/boostorg/mysql/pull/352?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---
