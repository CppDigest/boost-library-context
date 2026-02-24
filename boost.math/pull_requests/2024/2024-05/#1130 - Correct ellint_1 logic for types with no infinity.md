# #1130 Correct ellint_1 logic for types with no infinity. [Merged]

> Username: jzmaddock  
> Created at: 2024-05-03 17:07:27 UTC  
> Updated at: 2024-05-04 15:50:17 UTC  
> Merged at: 2024-05-04 07:52:51 UTC  
> Closed at: 2024-05-04 07:52:51 UTC  
> Url: https://github.com/boostorg/math/pull/1130  

Fixes multiprecision failures.

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2024-05-03 18:48:27 UTC  
> Updated_at: 2024-05-03 23:58:59 UTC  
> Url: https://github.com/boostorg/math/pull/1130#issuecomment-2093579076  

## [Codecov](https://app.codecov.io/gh/boostorg/math/pull/1130?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
All modified and coverable lines are covered by tests :white_check_mark:  
> Project coverage is 93.71%. Comparing base [(`435d103`)](https://app.codecov.io/gh/boostorg/math/commit/435d103a8953536629a41a2caba6b78a7cd0fa86?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) to head [(`3dafc07`)](https://app.codecov.io/gh/boostorg/math/pull/1130?dropdown=coverage&src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/math/pull/1130/graphs/tree.svg?width=650&height=150&src=pr&token=26IDb1OAa7&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/math/pull/1130?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@           Coverage Diff            @@  
##           develop    #1130   +/-   ##  
========================================  
  Coverage    93.71%   93.71%             
========================================  
  Files          771      771             
  Lines        61103    61105    +2       
========================================  
+ Hits         57264    57266    +2       
  Misses        3839     3839             
```  
  
  
| [Files](https://app.codecov.io/gh/boostorg/math/pull/1130?dropdown=coverage&src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/math/special\_functions/ellint\_1.hpp](https://app.codecov.io/gh/boostorg/math/pull/1130?src=pr&el=tree&filepath=include%2Fboost%2Fmath%2Fspecial_functions%2Fellint_1.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9tYXRoL3NwZWNpYWxfZnVuY3Rpb25zL2VsbGludF8xLmhwcA==) | `100.00% <100.00%> (ø)` | |  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/math/pull/1130?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/math/pull/1130?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [435d103...3dafc07](https://app.codecov.io/gh/boostorg/math/pull/1130?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---
