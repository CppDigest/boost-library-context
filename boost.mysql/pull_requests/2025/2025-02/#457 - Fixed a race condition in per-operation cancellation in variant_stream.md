# #457 Fixed a race condition in per-operation cancellation in variant_stream [Merged]

> Username: anarthal  
> Created at: 2025-02-25 19:48:22 UTC  
> Updated at: 2025-02-26 15:50:31 UTC  
> Merged at: 2025-02-26 15:49:51 UTC  
> Closed at: 2025-02-26 15:49:51 UTC  
> Url: https://github.com/boostorg/mysql/pull/457  

close #454

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2025-02-25 20:44:56 UTC  
> Updated_at: 2025-02-26 15:50:31 UTC  
> Url: https://github.com/boostorg/mysql/pull/457#issuecomment-2683244537  

## [Codecov](https://app.codecov.io/gh/boostorg/mysql/pull/457?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
All modified and coverable lines are covered by tests :white_check_mark:  
> Project coverage is 99.02%. Comparing base [(`e988c9a`)](https://app.codecov.io/gh/boostorg/mysql/commit/e988c9ad9e94766fca3845a63ef84a315ffc3513?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) to head [(`444ca7f`)](https://app.codecov.io/gh/boostorg/mysql/commit/444ca7fc9f4b9eeae2863fed0c56e4b2e5efccb9?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> Report is 1 commits behind head on develop.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/mysql/pull/457/graphs/tree.svg?width=650&height=150&src=pr&token=ajbbjm3OiO&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/mysql/pull/457?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@           Coverage Diff            @@  
##           develop     #457   +/-   ##  
========================================  
  Coverage    99.02%   99.02%             
========================================  
  Files          142      142             
  Lines         7260     7262    +2       
========================================  
+ Hits          7189     7191    +2       
  Misses          71       71             
```  
  
| [Files with missing lines](https://app.codecov.io/gh/boostorg/mysql/pull/457?dropdown=coverage&src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [...clude/boost/mysql/impl/internal/variant\_stream.hpp](https://app.codecov.io/gh/boostorg/mysql/pull/457?src=pr&el=tree&filepath=include%2Fboost%2Fmysql%2Fimpl%2Finternal%2Fvariant_stream.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9teXNxbC9pbXBsL2ludGVybmFsL3ZhcmlhbnRfc3RyZWFtLmhwcA==) | `100.00% <100.00%> (ø)` | |  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/mysql/pull/457?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/mysql/pull/457?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [e988c9a...444ca7f](https://app.codecov.io/gh/boostorg/mysql/pull/457?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---
