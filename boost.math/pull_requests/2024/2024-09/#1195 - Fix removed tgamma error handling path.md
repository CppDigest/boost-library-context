# #1195 Fix removed tgamma error handling path [Merged]

> Username: mborland  
> Created at: 2024-09-09 14:28:37 UTC  
> Updated at: 2024-09-09 16:57:32 UTC  
> Merged at: 2024-09-09 16:54:55 UTC  
> Closed at: 2024-09-09 16:54:55 UTC  
> Url: https://github.com/boostorg/math/pull/1195  

Closes: #1194   
  
Confirmed on device: https://github.com/cppalliance/cuda-math/pull/24

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2024-09-09 15:27:11 UTC  
> Updated_at: 2024-09-09 16:57:32 UTC  
> Url: https://github.com/boostorg/math/pull/1195#issuecomment-2338428304  

## [Codecov](https://app.codecov.io/gh/boostorg/math/pull/1195?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
Attention: Patch coverage is `93.93939%` with `2 lines` in your changes missing coverage. Please review.  
> Project coverage is 93.79%. Comparing base [(`cb06899`)](https://app.codecov.io/gh/boostorg/math/commit/cb068993561cb143da401d5cf4ad52efc4b6ea57?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) to head [(`9d13307`)](https://app.codecov.io/gh/boostorg/math/commit/9d133071d064857bd50bf85391c9218dab0ed16b?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> Report is 5 commits behind head on develop.  
  
| [Files with missing lines](https://app.codecov.io/gh/boostorg/math/pull/1195?dropdown=coverage&src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Patch % | Lines |  
|---|---|---|  
| [include/boost/math/special\_functions/gamma.hpp](https://app.codecov.io/gh/boostorg/math/pull/1195?src=pr&el=tree&filepath=include%2Fboost%2Fmath%2Fspecial_functions%2Fgamma.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9tYXRoL3NwZWNpYWxfZnVuY3Rpb25zL2dhbW1hLmhwcA==) | 87.50% | [2 Missing :warning: ](https://app.codecov.io/gh/boostorg/math/pull/1195?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) |  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/math/pull/1195/graphs/tree.svg?width=650&height=150&src=pr&token=26IDb1OAa7&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/math/pull/1195?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@           Coverage Diff            @@  
##           develop    #1195   +/-   ##  
========================================  
  Coverage    93.78%   93.79%             
========================================  
  Files          655      656    +1       
  Lines        53783    53804   +21       
========================================  
+ Hits         50440    50464   +24       
+ Misses        3343     3340    -3       
```  
  
| [Files with missing lines](https://app.codecov.io/gh/boostorg/math/pull/1195?dropdown=coverage&src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [test/git\_issue\_1194.cpp](https://app.codecov.io/gh/boostorg/math/pull/1195?src=pr&el=tree&filepath=test%2Fgit_issue_1194.cpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC9naXRfaXNzdWVfMTE5NC5jcHA=) | `100.00% <100.00%> (ø)` | |  
| [include/boost/math/special\_functions/gamma.hpp](https://app.codecov.io/gh/boostorg/math/pull/1195?src=pr&el=tree&filepath=include%2Fboost%2Fmath%2Fspecial_functions%2Fgamma.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9tYXRoL3NwZWNpYWxfZnVuY3Rpb25zL2dhbW1hLmhwcA==) | `92.14% <87.50%> (+0.04%)` | :arrow_up: |  
  
... and [1 file with indirect coverage changes](https://app.codecov.io/gh/boostorg/math/pull/1195/indirect-changes?src=pr&el=tree-more&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/math/pull/1195?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/math/pull/1195?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [cb06899...9d13307](https://app.codecov.io/gh/boostorg/math/pull/1195?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---
