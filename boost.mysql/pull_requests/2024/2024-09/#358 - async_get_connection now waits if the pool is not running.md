# #358 async_get_connection now waits if the pool is not running [Merged]

> Username: anarthal  
> Created at: 2024-09-30 17:07:37 UTC  
> Updated at: 2024-09-30 19:38:44 UTC  
> Merged at: 2024-09-30 19:38:30 UTC  
> Closed at: 2024-09-30 19:38:30 UTC  
> Url: https://github.com/boostorg/mysql/pull/358  

close #356

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2024-09-30 19:34:16 UTC  
> Updated_at: 2024-09-30 19:38:44 UTC  
> Url: https://github.com/boostorg/mysql/pull/358#issuecomment-2384000948  

## [Codecov](https://app.codecov.io/gh/boostorg/mysql/pull/358?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
All modified and coverable lines are covered by tests :white_check_mark:  
> Project coverage is 99.05%. Comparing base [(`e39c978`)](https://app.codecov.io/gh/boostorg/mysql/commit/e39c9785146274d8fbdb64151121c76b420edd52?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) to head [(`ccdb140`)](https://app.codecov.io/gh/boostorg/mysql/commit/ccdb1409ffa08638e7c9a3fb4f3e547949f4b6d2?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> Report is 1 commits behind head on develop.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/mysql/pull/358/graphs/tree.svg?width=650&height=150&src=pr&token=ajbbjm3OiO&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/mysql/pull/358?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@           Coverage Diff            @@  
##           develop     #358   +/-   ##  
========================================  
  Coverage    99.05%   99.05%             
========================================  
  Files          143      143             
  Lines         7184     7184             
========================================  
  Hits          7116     7116             
  Misses          68       68             
```  
  
| [Files with missing lines](https://app.codecov.io/gh/boostorg/mysql/pull/358?dropdown=coverage&src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/mysql/client\_errc.hpp](https://app.codecov.io/gh/boostorg/mysql/pull/358?src=pr&el=tree&filepath=include%2Fboost%2Fmysql%2Fclient_errc.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9teXNxbC9jbGllbnRfZXJyYy5ocHA=) | `100.00% <ø> (ø)` | |  
| [include/boost/mysql/connection\_pool.hpp](https://app.codecov.io/gh/boostorg/mysql/pull/358?src=pr&el=tree&filepath=include%2Fboost%2Fmysql%2Fconnection_pool.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9teXNxbC9jb25uZWN0aW9uX3Bvb2wuaHBw) | `100.00% <ø> (ø)` | |  
| [include/boost/mysql/impl/error\_categories.ipp](https://app.codecov.io/gh/boostorg/mysql/pull/358?src=pr&el=tree&filepath=include%2Fboost%2Fmysql%2Fimpl%2Ferror_categories.ipp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9teXNxbC9pbXBsL2Vycm9yX2NhdGVnb3JpZXMuaXBw) | `98.61% <ø> (ø)` | |  
| [.../internal/connection\_pool/connection\_pool\_impl.hpp](https://app.codecov.io/gh/boostorg/mysql/pull/358?src=pr&el=tree&filepath=include%2Fboost%2Fmysql%2Fimpl%2Finternal%2Fconnection_pool%2Fconnection_pool_impl.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9teXNxbC9pbXBsL2ludGVybmFsL2Nvbm5lY3Rpb25fcG9vbC9jb25uZWN0aW9uX3Bvb2xfaW1wbC5ocHA=) | `98.87% <100.00%> (ø)` | |  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/mysql/pull/358?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/mysql/pull/358?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [e39c978...ccdb140](https://app.codecov.io/gh/boostorg/mysql/pull/358?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---
