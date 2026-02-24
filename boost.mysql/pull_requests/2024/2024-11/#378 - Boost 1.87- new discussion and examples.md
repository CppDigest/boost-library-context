# #378 Boost 1.87: new discussion and examples [Merged]

> Username: anarthal  
> Created at: 2024-11-03 20:12:24 UTC  
> Updated at: 2024-11-05 05:04:17 UTC  
> Merged at: 2024-11-04 23:12:27 UTC  
> Closed at: 2024-11-04 23:12:27 UTC  
> Url: https://github.com/boostorg/mysql/pull/378  

close #365   
close #366

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2024-11-04 23:08:10 UTC  
> Updated_at: 2024-11-05 05:04:17 UTC  
> Url: https://github.com/boostorg/mysql/pull/378#issuecomment-2455884614  

## [Codecov](https://app.codecov.io/gh/boostorg/mysql/pull/378?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
All modified and coverable lines are covered by tests :white_check_mark:  
> Project coverage is 99.02%. Comparing base [(`c57103b`)](https://app.codecov.io/gh/boostorg/mysql/commit/c57103bb750c669c5e9a0b825303775bf6084432?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) to head [(`b780376`)](https://app.codecov.io/gh/boostorg/mysql/commit/b780376a23aa380915b38af61ee79aabaab17880?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> Report is 1 commits behind head on develop.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/mysql/pull/378/graphs/tree.svg?width=650&height=150&src=pr&token=ajbbjm3OiO&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/mysql/pull/378?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #378      +/-   ##  
===========================================  
- Coverage    99.05%   99.02%   -0.03%       
===========================================  
  Files          143      143                
  Lines         7161     7157       -4       
===========================================  
- Hits          7093     7087       -6       
- Misses          68       70       +2       
```  
  
| [Files with missing lines](https://app.codecov.io/gh/boostorg/mysql/pull/378?dropdown=coverage&src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/mysql/execution\_state.hpp](https://app.codecov.io/gh/boostorg/mysql/pull/378?src=pr&el=tree&filepath=include%2Fboost%2Fmysql%2Fexecution_state.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9teXNxbC9leGVjdXRpb25fc3RhdGUuaHBw) | `100.00% <ø> (ø)` | |  
| [include/boost/mysql/results.hpp](https://app.codecov.io/gh/boostorg/mysql/pull/378?src=pr&el=tree&filepath=include%2Fboost%2Fmysql%2Fresults.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9teXNxbC9yZXN1bHRzLmhwcA==) | `100.00% <ø> (ø)` | |  
| [include/boost/mysql/static\_execution\_state.hpp](https://app.codecov.io/gh/boostorg/mysql/pull/378?src=pr&el=tree&filepath=include%2Fboost%2Fmysql%2Fstatic_execution_state.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9teXNxbC9zdGF0aWNfZXhlY3V0aW9uX3N0YXRlLmhwcA==) | `100.00% <ø> (ø)` | |  
| [include/boost/mysql/static\_results.hpp](https://app.codecov.io/gh/boostorg/mysql/pull/378?src=pr&el=tree&filepath=include%2Fboost%2Fmysql%2Fstatic_results.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9teXNxbC9zdGF0aWNfcmVzdWx0cy5ocHA=) | `100.00% <ø> (ø)` | |  
  
... and [2 files with indirect coverage changes](https://app.codecov.io/gh/boostorg/mysql/pull/378/indirect-changes?src=pr&el=tree-more&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/mysql/pull/378?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/mysql/pull/378?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [c57103b...b780376](https://app.codecov.io/gh/boostorg/mysql/pull/378?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---
