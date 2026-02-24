# #331 Async ops now support cancel_after and similar tokens [Merged]

> Username: anarthal  
> Created at: 2024-08-08 15:56:46 UTC  
> Updated at: 2024-08-08 16:46:35 UTC  
> Merged at: 2024-08-08 16:46:34 UTC  
> Closed at: 2024-08-08 16:46:34 UTC  
> Url: https://github.com/boostorg/mysql/pull/331  

All initiations have now an associated executor.  
  
close #330

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2024-08-08 16:45:31 UTC  
> Url: https://github.com/boostorg/mysql/pull/331#issuecomment-2276249984  

## [Codecov](https://app.codecov.io/gh/boostorg/mysql/pull/331?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
All modified and coverable lines are covered by tests :white_check_mark:  
> Project coverage is 98.69%. Comparing base [(`a20fc3e`)](https://app.codecov.io/gh/boostorg/mysql/commit/a20fc3e5ea9d57ca7c96d494b27bf0de014c631f?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) to head [(`8399774`)](https://app.codecov.io/gh/boostorg/mysql/commit/83997740df6156074a8a87b368b3af9ff0b7690a?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/mysql/pull/331/graphs/tree.svg?width=650&height=150&src=pr&token=ajbbjm3OiO&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/mysql/pull/331?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #331      +/-   ##  
===========================================  
+ Coverage    98.60%   98.69%   +0.08%       
===========================================  
  Files          140      141       +1       
  Lines         7108     7115       +7       
===========================================  
+ Hits          7009     7022      +13       
+ Misses          99       93       -6       
```  
  
| [Files](https://app.codecov.io/gh/boostorg/mysql/pull/331?dropdown=coverage&src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/mysql/connection\_pool.hpp](https://app.codecov.io/gh/boostorg/mysql/pull/331?src=pr&el=tree&filepath=include%2Fboost%2Fmysql%2Fconnection_pool.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9teXNxbC9jb25uZWN0aW9uX3Bvb2wuaHBw) | `100.00% <100.00%> (ø)` | |  
| [include/boost/mysql/detail/async\_helpers.hpp](https://app.codecov.io/gh/boostorg/mysql/pull/331?src=pr&el=tree&filepath=include%2Fboost%2Fmysql%2Fdetail%2Fasync_helpers.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9teXNxbC9kZXRhaWwvYXN5bmNfaGVscGVycy5ocHA=) | `100.00% <100.00%> (ø)` | |  
| [include/boost/mysql/detail/connection\_impl.hpp](https://app.codecov.io/gh/boostorg/mysql/pull/331?src=pr&el=tree&filepath=include%2Fboost%2Fmysql%2Fdetail%2Fconnection_impl.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9teXNxbC9kZXRhaWwvY29ubmVjdGlvbl9pbXBsLmhwcA==) | `95.23% <100.00%> (+4.83%)` | :arrow_up: |  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/mysql/pull/331?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/mysql/pull/331?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [a20fc3e...8399774](https://app.codecov.io/gh/boostorg/mysql/pull/331?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---
