# #377 with_diagnostics now passes through unknown signatures [Merged]

> Username: anarthal  
> Created at: 2024-11-02 14:54:21 UTC  
> Updated at: 2024-11-03 00:38:54 UTC  
> Merged at: 2024-11-02 19:10:22 UTC  
> Closed at: 2024-11-02 19:10:22 UTC  
> Url: https://github.com/boostorg/mysql/pull/377  

This makes asio::as_tuple and asio::redirect_error usable as partial completion tokens with any_connection and connection_pool.  
Removed leftover comment in the pipeline example.

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2024-11-02 18:51:56 UTC  
> Updated_at: 2024-11-03 00:38:54 UTC  
> Url: https://github.com/boostorg/mysql/pull/377#issuecomment-2453093696  

## [Codecov](https://app.codecov.io/gh/boostorg/mysql/pull/377?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
All modified and coverable lines are covered by tests :white_check_mark:  
> Project coverage is 99.05%. Comparing base [(`272533c`)](https://app.codecov.io/gh/boostorg/mysql/commit/272533c9380b84babebe30b8bd31669ebc01023b?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) to head [(`a044e96`)](https://app.codecov.io/gh/boostorg/mysql/commit/a044e9698d874e05e0afa1f5513071fabd85da10?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> Report is 1 commits behind head on develop.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/mysql/pull/377/graphs/tree.svg?width=650&height=150&src=pr&token=ajbbjm3OiO&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/mysql/pull/377?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@           Coverage Diff            @@  
##           develop     #377   +/-   ##  
========================================  
  Coverage    99.04%   99.05%             
========================================  
  Files          143      143             
  Lines         7157     7161    +4       
========================================  
+ Hits          7089     7093    +4       
  Misses          68       68             
```  
  
| [Files with missing lines](https://app.codecov.io/gh/boostorg/mysql/pull/377?dropdown=coverage&src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/mysql/impl/with\_diagnostics.hpp](https://app.codecov.io/gh/boostorg/mysql/pull/377?src=pr&el=tree&filepath=include%2Fboost%2Fmysql%2Fimpl%2Fwith_diagnostics.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9teXNxbC9pbXBsL3dpdGhfZGlhZ25vc3RpY3MuaHBw) | `100.00% <100.00%> (ø)` | |  
| [include/boost/mysql/with\_diagnostics.hpp](https://app.codecov.io/gh/boostorg/mysql/pull/377?src=pr&el=tree&filepath=include%2Fboost%2Fmysql%2Fwith_diagnostics.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9teXNxbC93aXRoX2RpYWdub3N0aWNzLmhwcA==) | `100.00% <ø> (ø)` | |  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/mysql/pull/377?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/mysql/pull/377?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [272533c...a044e96](https://app.codecov.io/gh/boostorg/mysql/pull/377?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---
