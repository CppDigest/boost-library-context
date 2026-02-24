# #319 as_netresult completion token for tests [Merged]

> Username: anarthal  
> Created at: 2024-07-19 15:01:25 UTC  
> Updated at: 2024-07-23 16:27:32 UTC  
> Merged at: 2024-07-23 16:27:30 UTC  
> Closed at: 2024-07-23 16:27:30 UTC  
> Url: https://github.com/boostorg/mysql/pull/319  

Restructured handshake integration tests  
Removed er_connection infrastructure  
Reworked network_result  
Greatly simplified integration tests

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2024-07-23 12:06:37 UTC  
> Url: https://github.com/boostorg/mysql/pull/319#issuecomment-2245064154  

## [Codecov](https://app.codecov.io/gh/boostorg/mysql/pull/319?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
All modified and coverable lines are covered by tests :white_check_mark:  
> Project coverage is 98.24%. Comparing base [(`0060494`)](https://app.codecov.io/gh/boostorg/mysql/commit/006049417033ca672e7893bfd4978a498d1c261a?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) to head [(`7d56535`)](https://app.codecov.io/gh/boostorg/mysql/commit/7d565357536447130409468c66a20f0dfd65b648?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/mysql/pull/319/graphs/tree.svg?width=650&height=150&src=pr&token=ajbbjm3OiO&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/mysql/pull/319?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #319      +/-   ##  
===========================================  
- Coverage    98.26%   98.24%   -0.02%       
===========================================  
  Files          128      129       +1       
  Lines         5872     5864       -8       
===========================================  
- Hits          5770     5761       -9       
- Misses         102      103       +1       
```  
  
| [Files](https://app.codecov.io/gh/boostorg/mysql/pull/319?dropdown=coverage&src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/mysql/detail/connection\_impl.hpp](https://app.codecov.io/gh/boostorg/mysql/pull/319?src=pr&el=tree&filepath=include%2Fboost%2Fmysql%2Fdetail%2Fconnection_impl.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9teXNxbC9kZXRhaWwvY29ubmVjdGlvbl9pbXBsLmhwcA==) | `90.75% <ø> (-1.05%)` | :arrow_down: |  
  
... and [7 files with indirect coverage changes](https://app.codecov.io/gh/boostorg/mysql/pull/319/indirect-changes?src=pr&el=tree-more&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/mysql/pull/319?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/mysql/pull/319?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [0060494...7d56535](https://app.codecov.io/gh/boostorg/mysql/pull/319?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---
