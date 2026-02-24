# #413 Fixed a race condition in per-operation cancellation in connection operations [Merged]

> Username: anarthal  
> Created at: 2025-02-11 17:18:17 UTC  
> Updated at: 2025-02-11 18:16:02 UTC  
> Merged at: 2025-02-11 18:15:51 UTC  
> Closed at: 2025-02-11 18:15:51 UTC  
> Url: https://github.com/boostorg/mysql/pull/413  

Now connection/any_connection async operations are cancelled even when the cancellation signal is emitted after an intermediate operation completes, but before its intermediate handler gets called.  
Restored a connection_id integration test that required this fix.  
  
close #199

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2025-02-11 18:09:23 UTC  
> Updated_at: 2025-02-11 18:15:58 UTC  
> Url: https://github.com/boostorg/mysql/pull/413#issuecomment-2651626958  

## [Codecov](https://app.codecov.io/gh/boostorg/mysql/pull/413?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
All modified and coverable lines are covered by tests :white_check_mark:  
> Project coverage is 98.95%. Comparing base [(`829dbf7`)](https://app.codecov.io/gh/boostorg/mysql/commit/829dbf775c6e515180b0c15ab7f76813a813635e?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) to head [(`ccd3517`)](https://app.codecov.io/gh/boostorg/mysql/commit/ccd3517fa6eb9473360ab2d897fd3d11d523fc86?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> Report is 2 commits behind head on develop.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/mysql/pull/413/graphs/tree.svg?width=650&height=150&src=pr&token=ajbbjm3OiO&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/mysql/pull/413?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@           Coverage Diff            @@  
##           develop     #413   +/-   ##  
========================================  
  Coverage    98.95%   98.95%             
========================================  
  Files          142      142             
  Lines         7188     7192    +4       
========================================  
+ Hits          7113     7117    +4       
  Misses          75       75             
```  
  
| [Files with missing lines](https://app.codecov.io/gh/boostorg/mysql/pull/413?dropdown=coverage&src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/mysql/detail/engine\_impl.hpp](https://app.codecov.io/gh/boostorg/mysql/pull/413?src=pr&el=tree&filepath=include%2Fboost%2Fmysql%2Fdetail%2Fengine_impl.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9teXNxbC9kZXRhaWwvZW5naW5lX2ltcGwuaHBw) | `97.14% <100.00%> (+0.17%)` | :arrow_up: |  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/mysql/pull/413?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/mysql/pull/413?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [829dbf7...ccd3517](https://app.codecov.io/gh/boostorg/mysql/pull/413?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---
