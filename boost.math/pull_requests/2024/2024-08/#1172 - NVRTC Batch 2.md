# #1172 NVRTC Batch 2 [Merged]

> Username: mborland  
> Created at: 2024-08-08 12:59:38 UTC  
> Updated at: 2024-08-12 14:41:32 UTC  
> Merged at: 2024-08-12 14:41:29 UTC  
> Closed at: 2024-08-12 14:41:29 UTC  
> Url: https://github.com/boostorg/math/pull/1172  

Adds support to: sign functions, `lgamma`, and all the fpclassify functions.  
  
Internally this adds a complete implementation of `<type_traits>` that is usable in both CUDA and non-CUDA environments. I also added the C++17 overloads because they are ergonomic. I also had to add definitions of the macros that fpclassify checks against (e.g. `FP_NAN` is now `BOOST_MATH_FP_NAN`) since they are not provided in the NVRCT environment.   
  
Downstream run with completed CI can be found here: https://github.com/cppalliance/cuda-math/pull/9  
  
Based on the conversation from the boost dev mailing list ports of all the utilities that are getting built in path will be added here: https://github.com/cppalliance/GPUtils so that the wheel does not need to be reinvented again. @steppi that repo may or may not be useful to you in the future.

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2024-08-08 14:18:16 UTC  
> Updated_at: 2024-08-09 08:57:13 UTC  
> Url: https://github.com/boostorg/math/pull/1172#issuecomment-2275949668  

## [Codecov](https://app.codecov.io/gh/boostorg/math/pull/1172?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
All modified and coverable lines are covered by tests :white_check_mark:  
> Project coverage is 94.08%. Comparing base [(`2a4351e`)](https://app.codecov.io/gh/boostorg/math/commit/2a4351eb80a18c9abb5a40b96917df073f0939ae?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) to head [(`37875f2`)](https://app.codecov.io/gh/boostorg/math/commit/37875f2a8ba08251a37d7900bf6d366ebcefc65c?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/math/pull/1172/graphs/tree.svg?width=650&height=150&src=pr&token=26IDb1OAa7&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/math/pull/1172?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@             Coverage Diff             @@  
##           develop    #1172      +/-   ##  
===========================================  
+ Coverage    94.06%   94.08%   +0.02%       
===========================================  
  Files          780      780                
  Lines        65797    65797                
===========================================  
+ Hits         61890    61904      +14       
+ Misses        3907     3893      -14       
```  
  
| [Files](https://app.codecov.io/gh/boostorg/math/pull/1172?dropdown=coverage&src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/math/special\_functions/cbrt.hpp](https://app.codecov.io/gh/boostorg/math/pull/1172?src=pr&el=tree&filepath=include%2Fboost%2Fmath%2Fspecial_functions%2Fcbrt.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9tYXRoL3NwZWNpYWxfZnVuY3Rpb25zL2NicnQuaHBw) | `100.00% <ø> (ø)` | |  
| [...nclude/boost/math/special\_functions/fpclassify.hpp](https://app.codecov.io/gh/boostorg/math/pull/1172?src=pr&el=tree&filepath=include%2Fboost%2Fmath%2Fspecial_functions%2Ffpclassify.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9tYXRoL3NwZWNpYWxfZnVuY3Rpb25zL2ZwY2xhc3NpZnkuaHBw) | `98.68% <ø> (ø)` | |  
| [include/boost/math/special\_functions/gamma.hpp](https://app.codecov.io/gh/boostorg/math/pull/1172?src=pr&el=tree&filepath=include%2Fboost%2Fmath%2Fspecial_functions%2Fgamma.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9tYXRoL3NwZWNpYWxfZnVuY3Rpb25zL2dhbW1hLmhwcA==) | `91.75% <ø> (ø)` | |  
| [include/boost/math/special\_functions/sign.hpp](https://app.codecov.io/gh/boostorg/math/pull/1172?src=pr&el=tree&filepath=include%2Fboost%2Fmath%2Fspecial_functions%2Fsign.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9tYXRoL3NwZWNpYWxfZnVuY3Rpb25zL3NpZ24uaHBw) | `100.00% <ø> (ø)` | |  
  
... and [1 file with indirect coverage changes](https://app.codecov.io/gh/boostorg/math/pull/1172/indirect-changes?src=pr&el=tree-more&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/math/pull/1172?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/math/pull/1172?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [2a4351e...37875f2](https://app.codecov.io/gh/boostorg/math/pull/1172?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---

## Comment 2

> Username: mborland  
> Created_at: 2024-08-12 14:41:17 UTC  
> Url: https://github.com/boostorg/math/pull/1172#issuecomment-2284172513  

Only failure is from Drone failure to clone so this should be safe to merge.

---
