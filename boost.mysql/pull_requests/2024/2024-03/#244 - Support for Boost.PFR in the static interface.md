# #244 Support for Boost.PFR in the static interface [Merged]

> Username: anarthal  
> Created at: 2024-03-21 22:19:52 UTC  
> Updated at: 2024-04-18 13:52:01 UTC  
> Merged at: 2024-04-18 13:52:00 UTC  
> Closed at: 2024-04-18 13:52:00 UTC  
> Url: https://github.com/boostorg/mysql/pull/244  

close #185

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2024-03-22 13:41:20 UTC  
> Updated_at: 2024-03-27 13:14:47 UTC  
> Url: https://github.com/boostorg/mysql/pull/244#issuecomment-2015135459  

## [Codecov](https://app.codecov.io/gh/boostorg/mysql/pull/244?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
All modified and coverable lines are covered by tests :white_check_mark:  
> Project coverage is 98.35%. Comparing base [(`6e2b833`)](https://app.codecov.io/gh/boostorg/mysql/commit/6e2b833a0520fa318edca6c44e8be09d1aceecc1?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) to head [(`74cebad`)](https://app.codecov.io/gh/boostorg/mysql/pull/244?dropdown=coverage&src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/mysql/pull/244/graphs/tree.svg?width=650&height=150&src=pr&token=ajbbjm3OiO&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/mysql/pull/244?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@           Coverage Diff            @@  
##           develop     #244   +/-   ##  
========================================  
  Coverage    98.34%   98.35%             
========================================  
  Files          129      129             
  Lines         6359     6369   +10       
========================================  
+ Hits          6254     6264   +10       
  Misses         105      105             
```  
  
| [Files](https://app.codecov.io/gh/boostorg/mysql/pull/244?dropdown=coverage&src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/mysql/any\_connection.hpp](https://app.codecov.io/gh/boostorg/mysql/pull/244?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9teXNxbC9hbnlfY29ubmVjdGlvbi5ocHA=) | `98.81% <100.00%> (ø)` | |  
| [include/boost/mysql/connection.hpp](https://app.codecov.io/gh/boostorg/mysql/pull/244?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9teXNxbC9jb25uZWN0aW9uLmhwcA==) | `99.05% <100.00%> (ø)` | |  
| [include/boost/mysql/detail/connection\_impl.hpp](https://app.codecov.io/gh/boostorg/mysql/pull/244?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9teXNxbC9kZXRhaWwvY29ubmVjdGlvbl9pbXBsLmhwcA==) | `96.26% <100.00%> (+0.03%)` | :arrow_up: |  
| [...xecution\_processor/static\_execution\_state\_impl.hpp](https://app.codecov.io/gh/boostorg/mysql/pull/244?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9teXNxbC9kZXRhaWwvZXhlY3V0aW9uX3Byb2Nlc3Nvci9zdGF0aWNfZXhlY3V0aW9uX3N0YXRlX2ltcGwuaHBw) | `100.00% <100.00%> (ø)` | |  
| [...detail/execution\_processor/static\_results\_impl.hpp](https://app.codecov.io/gh/boostorg/mysql/pull/244?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9teXNxbC9kZXRhaWwvZXhlY3V0aW9uX3Byb2Nlc3Nvci9zdGF0aWNfcmVzdWx0c19pbXBsLmhwcA==) | `100.00% <100.00%> (ø)` | |  
| [...oost/mysql/detail/typing/readable\_field\_traits.hpp](https://app.codecov.io/gh/boostorg/mysql/pull/244?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9teXNxbC9kZXRhaWwvdHlwaW5nL3JlYWRhYmxlX2ZpZWxkX3RyYWl0cy5ocHA=) | `100.00% <ø> (ø)` | |  
| [include/boost/mysql/detail/typing/row\_traits.hpp](https://app.codecov.io/gh/boostorg/mysql/pull/244?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9teXNxbC9kZXRhaWwvdHlwaW5nL3Jvd190cmFpdHMuaHBw) | `100.00% <100.00%> (ø)` | |  
| [include/boost/mysql/impl/pfr.hpp](https://app.codecov.io/gh/boostorg/mysql/pull/244?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9teXNxbC9pbXBsL3Bmci5ocHA=) | `100.00% <100.00%> (ø)` | |  
| [include/boost/mysql/static\_results.hpp](https://app.codecov.io/gh/boostorg/mysql/pull/244?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9teXNxbC9zdGF0aWNfcmVzdWx0cy5ocHA=) | `100.00% <ø> (ø)` | |  
  
... and [1 file with indirect coverage changes](https://app.codecov.io/gh/boostorg/mysql/pull/244/indirect-changes?src=pr&el=tree-more&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/mysql/pull/244?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/mysql/pull/244?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [6e2b833...74cebad](https://app.codecov.io/gh/boostorg/mysql/pull/244?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---
