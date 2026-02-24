# #267 Marked constants as inline constexpr [Merged]

> Username: anarthal  
> Created at: 2024-05-16 10:50:49 UTC  
> Updated at: 2024-05-16 15:13:12 UTC  
> Merged at: 2024-05-16 15:13:12 UTC  
> Closed at: 2024-05-16 15:13:12 UTC  
> Url: https://github.com/boostorg/mysql/pull/267  

close #265

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2024-05-16 11:44:45 UTC  
> Updated_at: 2024-05-16 12:30:44 UTC  
> Url: https://github.com/boostorg/mysql/pull/267#issuecomment-2115009904  

## [Codecov](https://app.codecov.io/gh/boostorg/mysql/pull/267?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
All modified and coverable lines are covered by tests :white_check_mark:  
> Project coverage is 98.51%. Comparing base [(`c897352`)](https://app.codecov.io/gh/boostorg/mysql/commit/c89735273b0f114dc2fd05305a583e7b32e87ef6?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) to head [(`f0b96b5`)](https://app.codecov.io/gh/boostorg/mysql/pull/267?dropdown=coverage&src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/mysql/pull/267/graphs/tree.svg?width=650&height=150&src=pr&token=ajbbjm3OiO&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/mysql/pull/267?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@           Coverage Diff            @@  
##           develop     #267   +/-   ##  
========================================  
  Coverage    98.51%   98.51%             
========================================  
  Files          132      132             
  Lines         6251     6251             
========================================  
  Hits          6158     6158             
  Misses          93       93             
```  
  
| [Files](https://app.codecov.io/gh/boostorg/mysql/pull/267?dropdown=coverage&src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/mysql/buffer\_params.hpp](https://app.codecov.io/gh/boostorg/mysql/pull/267?src=pr&el=tree&filepath=include%2Fboost%2Fmysql%2Fbuffer_params.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9teXNxbC9idWZmZXJfcGFyYW1zLmhwcA==) | `100.00% <ø> (ø)` | |  
| [include/boost/mysql/date.hpp](https://app.codecov.io/gh/boostorg/mysql/pull/267?src=pr&el=tree&filepath=include%2Fboost%2Fmysql%2Fdate.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9teXNxbC9kYXRlLmhwcA==) | `100.00% <ø> (ø)` | |  
| [include/boost/mysql/datetime.hpp](https://app.codecov.io/gh/boostorg/mysql/pull/267?src=pr&el=tree&filepath=include%2Fboost%2Fmysql%2Fdatetime.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9teXNxbC9kYXRldGltZS5ocHA=) | `100.00% <ø> (ø)` | |  
| [include/boost/mysql/detail/datetime.hpp](https://app.codecov.io/gh/boostorg/mysql/pull/267?src=pr&el=tree&filepath=include%2Fboost%2Fmysql%2Fdetail%2Fdatetime.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9teXNxbC9kZXRhaWwvZGF0ZXRpbWUuaHBw) | `100.00% <ø> (ø)` | |  
| [.../mysql/detail/execution\_processor/results\_impl.hpp](https://app.codecov.io/gh/boostorg/mysql/pull/267?src=pr&el=tree&filepath=include%2Fboost%2Fmysql%2Fdetail%2Fexecution_processor%2Fresults_impl.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9teXNxbC9kZXRhaWwvZXhlY3V0aW9uX3Byb2Nlc3Nvci9yZXN1bHRzX2ltcGwuaHBw) | `100.00% <ø> (ø)` | |  
| [...xecution\_processor/static\_execution\_state\_impl.hpp](https://app.codecov.io/gh/boostorg/mysql/pull/267?src=pr&el=tree&filepath=include%2Fboost%2Fmysql%2Fdetail%2Fexecution_processor%2Fstatic_execution_state_impl.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9teXNxbC9kZXRhaWwvZXhlY3V0aW9uX3Byb2Nlc3Nvci9zdGF0aWNfZXhlY3V0aW9uX3N0YXRlX2ltcGwuaHBw) | `100.00% <ø> (ø)` | |  
| [...detail/execution\_processor/static\_results\_impl.hpp](https://app.codecov.io/gh/boostorg/mysql/pull/267?src=pr&el=tree&filepath=include%2Fboost%2Fmysql%2Fdetail%2Fexecution_processor%2Fstatic_results_impl.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9teXNxbC9kZXRhaWwvZXhlY3V0aW9uX3Byb2Nlc3Nvci9zdGF0aWNfcmVzdWx0c19pbXBsLmhwcA==) | `100.00% <ø> (ø)` | |  
| [include/boost/mysql/detail/typing/pos\_map.hpp](https://app.codecov.io/gh/boostorg/mysql/pull/267?src=pr&el=tree&filepath=include%2Fboost%2Fmysql%2Fdetail%2Ftyping%2Fpos_map.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9teXNxbC9kZXRhaWwvdHlwaW5nL3Bvc19tYXAuaHBw) | `100.00% <ø> (ø)` | |  
| [...oost/mysql/detail/typing/readable\_field\_traits.hpp](https://app.codecov.io/gh/boostorg/mysql/pull/267?src=pr&el=tree&filepath=include%2Fboost%2Fmysql%2Fdetail%2Ftyping%2Freadable_field_traits.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9teXNxbC9kZXRhaWwvdHlwaW5nL3JlYWRhYmxlX2ZpZWxkX3RyYWl0cy5ocHA=) | `100.00% <ø> (ø)` | |  
| [include/boost/mysql/detail/typing/row\_traits.hpp](https://app.codecov.io/gh/boostorg/mysql/pull/267?src=pr&el=tree&filepath=include%2Fboost%2Fmysql%2Fdetail%2Ftyping%2Frow_traits.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9teXNxbC9kZXRhaWwvdHlwaW5nL3Jvd190cmFpdHMuaHBw) | `100.00% <ø> (ø)` | |  
| ... and [4 more](https://app.codecov.io/gh/boostorg/mysql/pull/267?src=pr&el=tree-more&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | |  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/mysql/pull/267?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/mysql/pull/267?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [c897352...f0b96b5](https://app.codecov.io/gh/boostorg/mysql/pull/267?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---
