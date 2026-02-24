# #355 Connections now support immediate executors [Merged]

> Username: anarthal  
> Created at: 2024-09-27 17:40:11 UTC  
> Updated at: 2024-10-03 18:29:23 UTC  
> Merged at: 2024-10-03 18:19:52 UTC  
> Closed at: 2024-10-03 18:19:52 UTC  
> Url: https://github.com/boostorg/mysql/pull/355  

Immediate completions in connection and any_connection are now correctly dispatched to the token's immediate executor using asio::async_immediate instead of plain asio::post.  
Added a section on executors in the reference docs of async functions in connection and any_connection  
  
close #301

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2024-10-03 18:18:16 UTC  
> Updated_at: 2024-10-03 18:29:23 UTC  
> Url: https://github.com/boostorg/mysql/pull/355#issuecomment-2392039392  

## [Codecov](https://app.codecov.io/gh/boostorg/mysql/pull/355?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
All modified and coverable lines are covered by tests :white_check_mark:  
> Project coverage is 99.05%. Comparing base [(`7ef6ff8`)](https://app.codecov.io/gh/boostorg/mysql/commit/7ef6ff87732f9828152c4cc7577b46efd3003f6b?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) to head [(`c1b3c05`)](https://app.codecov.io/gh/boostorg/mysql/commit/c1b3c05230798538a62c9c414723bacc2a2a82fc?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> Report is 1 commits behind head on develop.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/mysql/pull/355/graphs/tree.svg?width=650&height=150&src=pr&token=ajbbjm3OiO&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/mysql/pull/355?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@           Coverage Diff            @@  
##           develop     #355   +/-   ##  
========================================  
  Coverage    99.05%   99.05%             
========================================  
  Files          143      143             
  Lines         7180     7180             
========================================  
  Hits          7112     7112             
  Misses          68       68             
```  
  
| [Files with missing lines](https://app.codecov.io/gh/boostorg/mysql/pull/355?dropdown=coverage&src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/mysql/any\_connection.hpp](https://app.codecov.io/gh/boostorg/mysql/pull/355?src=pr&el=tree&filepath=include%2Fboost%2Fmysql%2Fany_connection.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9teXNxbC9hbnlfY29ubmVjdGlvbi5ocHA=) | `100.00% <ø> (ø)` | |  
| [include/boost/mysql/connection.hpp](https://app.codecov.io/gh/boostorg/mysql/pull/355?src=pr&el=tree&filepath=include%2Fboost%2Fmysql%2Fconnection.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9teXNxbC9jb25uZWN0aW9uLmhwcA==) | `98.90% <ø> (ø)` | |  
| [include/boost/mysql/detail/engine\_impl.hpp](https://app.codecov.io/gh/boostorg/mysql/pull/355?src=pr&el=tree&filepath=include%2Fboost%2Fmysql%2Fdetail%2Fengine_impl.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9teXNxbC9kZXRhaWwvZW5naW5lX2ltcGwuaHBw) | `97.01% <ø> (ø)` | |  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/mysql/pull/355?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/mysql/pull/355?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [7ef6ff8...c1b3c05](https://app.codecov.io/gh/boostorg/mysql/pull/355?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---
