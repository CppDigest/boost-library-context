# #363 sequence now returns an owning type [Merged]

> Username: anarthal  
> Created at: 2024-10-04 15:49:08 UTC  
> Updated at: 2024-10-11 20:17:28 UTC  
> Merged at: 2024-10-11 20:17:17 UTC  
> Closed at: 2024-10-11 20:17:17 UTC  
> Url: https://github.com/boostorg/mysql/pull/363  

Renamed format_sequence_view to format_sequence  
format_sequence members are now part of the public API  
Moved sequence to a separate header  
Added sequence_range_t  
  
close #359

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2024-10-11 17:49:22 UTC  
> Updated_at: 2024-10-11 20:17:28 UTC  
> Url: https://github.com/boostorg/mysql/pull/363#issuecomment-2407876417  

## [Codecov](https://app.codecov.io/gh/boostorg/mysql/pull/363?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
All modified and coverable lines are covered by tests :white_check_mark:  
> Project coverage is 99.04%. Comparing base [(`41f07d2`)](https://app.codecov.io/gh/boostorg/mysql/commit/41f07d2e1e95aff9826b2958e2f13761af74488b?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) to head [(`9debf1e`)](https://app.codecov.io/gh/boostorg/mysql/commit/9debf1e8ec29dc918bea1403cf12d5b91ef773d4?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> Report is 1 commits behind head on develop.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/mysql/pull/363/graphs/tree.svg?width=650&height=150&src=pr&token=ajbbjm3OiO&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/mysql/pull/363?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@           Coverage Diff            @@  
##           develop     #363   +/-   ##  
========================================  
  Coverage    99.04%   99.04%             
========================================  
  Files          141      143    +2       
  Lines         7140     7154   +14       
========================================  
+ Hits          7072     7086   +14       
  Misses          68       68             
```  
  
| [Files with missing lines](https://app.codecov.io/gh/boostorg/mysql/pull/363?dropdown=coverage&src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/mysql/detail/format\_sql.hpp](https://app.codecov.io/gh/boostorg/mysql/pull/363?src=pr&el=tree&filepath=include%2Fboost%2Fmysql%2Fdetail%2Fformat_sql.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9teXNxbC9kZXRhaWwvZm9ybWF0X3NxbC5ocHA=) | `100.00% <ø> (ø)` | |  
| [include/boost/mysql/detail/sequence.hpp](https://app.codecov.io/gh/boostorg/mysql/pull/363?src=pr&el=tree&filepath=include%2Fboost%2Fmysql%2Fdetail%2Fsequence.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9teXNxbC9kZXRhaWwvc2VxdWVuY2UuaHBw) | `100.00% <100.00%> (ø)` | |  
| [include/boost/mysql/format\_sql.hpp](https://app.codecov.io/gh/boostorg/mysql/pull/363?src=pr&el=tree&filepath=include%2Fboost%2Fmysql%2Fformat_sql.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9teXNxbC9mb3JtYXRfc3FsLmhwcA==) | `100.00% <ø> (ø)` | |  
| [include/boost/mysql/impl/format\_sql.hpp](https://app.codecov.io/gh/boostorg/mysql/pull/363?src=pr&el=tree&filepath=include%2Fboost%2Fmysql%2Fimpl%2Fformat_sql.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9teXNxbC9pbXBsL2Zvcm1hdF9zcWwuaHBw) | `100.00% <100.00%> (ø)` | |  
| [include/boost/mysql/impl/with\_params.hpp](https://app.codecov.io/gh/boostorg/mysql/pull/363?src=pr&el=tree&filepath=include%2Fboost%2Fmysql%2Fimpl%2Fwith_params.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9teXNxbC9pbXBsL3dpdGhfcGFyYW1zLmhwcA==) | `100.00% <ø> (ø)` | |  
| [include/boost/mysql/sequence.hpp](https://app.codecov.io/gh/boostorg/mysql/pull/363?src=pr&el=tree&filepath=include%2Fboost%2Fmysql%2Fsequence.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9teXNxbC9zZXF1ZW5jZS5ocHA=) | `100.00% <100.00%> (ø)` | |  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/mysql/pull/363?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/mysql/pull/363?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [41f07d2...9debf1e](https://app.codecov.io/gh/boostorg/mysql/pull/363?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---
