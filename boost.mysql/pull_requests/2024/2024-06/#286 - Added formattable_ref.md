# #286 Added formattable_ref [Merged]

> Username: anarthal  
> Created at: 2024-06-22 15:33:16 UTC  
> Updated at: 2024-06-23 16:35:27 UTC  
> Merged at: 2024-06-23 16:35:26 UTC  
> Closed at: 2024-06-23 16:35:26 UTC  
> Url: https://github.com/boostorg/mysql/pull/286  

format_context_base::append_value now uses formattable_ref instead of being a template  
  
close #284

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2024-06-22 16:19:43 UTC  
> Updated_at: 2024-06-23 15:53:13 UTC  
> Url: https://github.com/boostorg/mysql/pull/286#issuecomment-2184087975  

## [Codecov](https://app.codecov.io/gh/boostorg/mysql/pull/286?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
All modified and coverable lines are covered by tests :white_check_mark:  
> Project coverage is 98.48%. Comparing base [(`80a8618`)](https://app.codecov.io/gh/boostorg/mysql/commit/80a86189e9cf1523158705ebf0de62a5d418f450?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) to head [(`4582da0`)](https://app.codecov.io/gh/boostorg/mysql/commit/4582da024c7ca1ee10cecfe5ad5f0f36ee19f8e1?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/mysql/pull/286/graphs/tree.svg?width=650&height=150&src=pr&token=ajbbjm3OiO&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/mysql/pull/286?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #286      +/-   ##  
===========================================  
- Coverage    98.48%   98.48%   -0.01%       
===========================================  
  Files          135      136       +1       
  Lines         6554     6551       -3       
===========================================  
- Hits          6455     6452       -3       
  Misses          99       99                
```  
  
| [Files](https://app.codecov.io/gh/boostorg/mysql/pull/286?dropdown=coverage&src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/mysql/detail/format\_sql.hpp](https://app.codecov.io/gh/boostorg/mysql/pull/286?src=pr&el=tree&filepath=include%2Fboost%2Fmysql%2Fdetail%2Fformat_sql.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9teXNxbC9kZXRhaWwvZm9ybWF0X3NxbC5ocHA=) | `100.00% <100.00%> (ø)` | |  
| [include/boost/mysql/format\_sql.hpp](https://app.codecov.io/gh/boostorg/mysql/pull/286?src=pr&el=tree&filepath=include%2Fboost%2Fmysql%2Fformat_sql.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9teXNxbC9mb3JtYXRfc3FsLmhwcA==) | `100.00% <100.00%> (ø)` | |  
| [include/boost/mysql/impl/format\_sql.hpp](https://app.codecov.io/gh/boostorg/mysql/pull/286?src=pr&el=tree&filepath=include%2Fboost%2Fmysql%2Fimpl%2Fformat_sql.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9teXNxbC9pbXBsL2Zvcm1hdF9zcWwuaHBw) | `100.00% <100.00%> (ø)` | |  
| [include/boost/mysql/impl/format\_sql.ipp](https://app.codecov.io/gh/boostorg/mysql/pull/286?src=pr&el=tree&filepath=include%2Fboost%2Fmysql%2Fimpl%2Fformat_sql.ipp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9teXNxbC9pbXBsL2Zvcm1hdF9zcWwuaXBw) | `99.12% <100.00%> (ø)` | |  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/mysql/pull/286?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/mysql/pull/286?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [80a8618...4582da0](https://app.codecov.io/gh/boostorg/mysql/pull/286?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---
