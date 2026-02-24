# #1182 NVRTC Batch 6 [Merged]

> Username: mborland  
> Created at: 2024-08-16 13:46:53 UTC  
> Updated at: 2024-08-19 17:38:35 UTC  
> Merged at: 2024-08-19 17:38:09 UTC  
> Closed at: 2024-08-19 17:38:09 UTC  
> Url: https://github.com/boostorg/math/pull/1182  

Adds support for `modf` (ours is different than libcu++), `trigamma`, and two arg `beta`. This brings NVRTC support up to par with NVCC and SYCL, so this will be the last NVRTC only PR. Moving forward the PRs will add unified support.   
  
Adds new workaround header `<boost/math/tools/cstdint.hpp>` to provide fixed width type definitions and defines `boost::math::size_t` to `unsigned long` on CUDA platforms because that type is missing. NVIDIA has been producing only 64-bit devices since 2012 so this size type is a safe assumption.  
  
Completed CI runs can be found: https://github.com/cppalliance/cuda-math/pull/15

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2024-08-16 15:00:51 UTC  
> Updated_at: 2024-08-19 17:38:35 UTC  
> Url: https://github.com/boostorg/math/pull/1182#issuecomment-2293673058  

## [Codecov](https://app.codecov.io/gh/boostorg/math/pull/1182?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
Attention: Patch coverage is `91.93548%` with `5 lines` in your changes missing coverage. Please review.  
> Project coverage is 94.06%. Comparing base [(`54e5acf`)](https://app.codecov.io/gh/boostorg/math/commit/54e5acf7faeab1400031d84bbfb2c46e81d82442?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) to head [(`1add05b`)](https://app.codecov.io/gh/boostorg/math/commit/1add05ba5ec36ac006f96dc16d822119885851bd?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> Report is 19 commits behind head on develop.  
  
| [Files](https://app.codecov.io/gh/boostorg/math/pull/1182?dropdown=coverage&src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Patch % | Lines |  
|---|---|---|  
| [include/boost/math/special\_functions/lanczos.hpp](https://app.codecov.io/gh/boostorg/math/pull/1182?src=pr&el=tree&filepath=include%2Fboost%2Fmath%2Fspecial_functions%2Flanczos.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9tYXRoL3NwZWNpYWxfZnVuY3Rpb25zL2xhbmN6b3MuaHBw) | 80.76% | [5 Missing :warning: ](https://app.codecov.io/gh/boostorg/math/pull/1182?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) |  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/math/pull/1182/graphs/tree.svg?width=650&height=150&src=pr&token=26IDb1OAa7&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/math/pull/1182?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@             Coverage Diff             @@  
##           develop    #1182      +/-   ##  
===========================================  
- Coverage    94.08%   94.06%   -0.02%       
===========================================  
  Files          780      780                
  Lines        65796    65796                
===========================================  
- Hits         61903    61891      -12       
- Misses        3893     3905      +12       
```  
  
| [Files](https://app.codecov.io/gh/boostorg/math/pull/1182?dropdown=coverage&src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/math/policies/error\_handling.hpp](https://app.codecov.io/gh/boostorg/math/pull/1182?src=pr&el=tree&filepath=include%2Fboost%2Fmath%2Fpolicies%2Ferror_handling.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9tYXRoL3BvbGljaWVzL2Vycm9yX2hhbmRsaW5nLmhwcA==) | `83.73% <ø> (ø)` | |  
| [include/boost/math/policies/policy.hpp](https://app.codecov.io/gh/boostorg/math/pull/1182?src=pr&el=tree&filepath=include%2Fboost%2Fmath%2Fpolicies%2Fpolicy.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9tYXRoL3BvbGljaWVzL3BvbGljeS5ocHA=) | `95.83% <100.00%> (ø)` | |  
| [include/boost/math/special\_functions/beta.hpp](https://app.codecov.io/gh/boostorg/math/pull/1182?src=pr&el=tree&filepath=include%2Fboost%2Fmath%2Fspecial_functions%2Fbeta.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9tYXRoL3NwZWNpYWxfZnVuY3Rpb25zL2JldGEuaHBw) | `100.00% <100.00%> (ø)` | |  
| [include/boost/math/special\_functions/gamma.hpp](https://app.codecov.io/gh/boostorg/math/pull/1182?src=pr&el=tree&filepath=include%2Fboost%2Fmath%2Fspecial_functions%2Fgamma.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9tYXRoL3NwZWNpYWxfZnVuY3Rpb25zL2dhbW1hLmhwcA==) | `91.75% <ø> (ø)` | |  
| [include/boost/math/special\_functions/modf.hpp](https://app.codecov.io/gh/boostorg/math/pull/1182?src=pr&el=tree&filepath=include%2Fboost%2Fmath%2Fspecial_functions%2Fmodf.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9tYXRoL3NwZWNpYWxfZnVuY3Rpb25zL21vZGYuaHBw) | `100.00% <ø> (ø)` | |  
| [include/boost/math/special\_functions/pow.hpp](https://app.codecov.io/gh/boostorg/math/pull/1182?src=pr&el=tree&filepath=include%2Fboost%2Fmath%2Fspecial_functions%2Fpow.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9tYXRoL3NwZWNpYWxfZnVuY3Rpb25zL3Bvdy5ocHA=) | `100.00% <100.00%> (ø)` | |  
| [include/boost/math/special\_functions/trigamma.hpp](https://app.codecov.io/gh/boostorg/math/pull/1182?src=pr&el=tree&filepath=include%2Fboost%2Fmath%2Fspecial_functions%2Ftrigamma.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9tYXRoL3NwZWNpYWxfZnVuY3Rpb25zL3RyaWdhbW1hLmhwcA==) | `97.69% <100.00%> (ø)` | |  
| [include/boost/math/tools/fraction.hpp](https://app.codecov.io/gh/boostorg/math/pull/1182?src=pr&el=tree&filepath=include%2Fboost%2Fmath%2Ftools%2Ffraction.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9tYXRoL3Rvb2xzL2ZyYWN0aW9uLmhwcA==) | `91.04% <100.00%> (ø)` | |  
| [include/boost/math/tools/rational.hpp](https://app.codecov.io/gh/boostorg/math/pull/1182?src=pr&el=tree&filepath=include%2Fboost%2Fmath%2Ftools%2Frational.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9tYXRoL3Rvb2xzL3JhdGlvbmFsLmhwcA==) | `100.00% <100.00%> (ø)` | |  
| [include/boost/math/tools/roots.hpp](https://app.codecov.io/gh/boostorg/math/pull/1182?src=pr&el=tree&filepath=include%2Fboost%2Fmath%2Ftools%2Froots.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9tYXRoL3Rvb2xzL3Jvb3RzLmhwcA==) | `86.79% <ø> (ø)` | |  
| ... and [1 more](https://app.codecov.io/gh/boostorg/math/pull/1182?src=pr&el=tree-more&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | |  
  
... and [1 file with indirect coverage changes](https://app.codecov.io/gh/boostorg/math/pull/1182/indirect-changes?src=pr&el=tree-more&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/math/pull/1182?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/math/pull/1182?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [54e5acf...1add05b](https://app.codecov.io/gh/boostorg/math/pull/1182?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---
