# #1187 GPU Batch 7 [Merged]

> Username: mborland  
> Created at: 2024-08-27 19:47:57 UTC  
> Updated at: 2024-08-30 16:23:52 UTC  
> Merged at: 2024-08-30 16:23:02 UTC  
> Closed at: 2024-08-30 16:23:02 UTC  
> Url: https://github.com/boostorg/math/pull/1187  

This begins to remove earlier workarounds for NVRTC now that support is more robust. In this PR workarounds were removed from gamma so now instead of having just `tgamma`, and `lgamma` on device we have the whole gamma family including: `tgamma_ratio`, `tgamma_delta_ratio`, `gamma_p_inv`, `gamma_p_inva`, `gamma_q_inv`, `gamma_q_inva`, `gamma_p_derivative`, `igamma`, `igamma_inv`, `igamma_inva`. This additional support allows us to add GPU support to the chi squared distribution and weibull distributions. We also gain root finding support with toms748 now being device capable.  
  
This PR is a bit uglier than other because NVRTC does not really support forward declarations. `gamma.hpp` used these extensively so we either call the CUDA standard library where we can, or apply the correct pre-processing to call detail functions. A little bit of recursion was eliminated at the cost of additional dispatching functions.  
  
Completed device CI runs can be found here: https://github.com/cppalliance/cuda-math/pull/18.  
  
CC: @steppi, @dschmitz89, @izaid.

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2024-08-27 20:52:44 UTC  
> Updated_at: 2024-08-30 16:23:52 UTC  
> Url: https://github.com/boostorg/math/pull/1187#issuecomment-2313514901  

## [Codecov](https://app.codecov.io/gh/boostorg/math/pull/1187?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
Attention: Patch coverage is `95.54455%` with `9 lines` in your changes missing coverage. Please review.  
> Project coverage is 94.06%. Comparing base [(`66b362a`)](https://app.codecov.io/gh/boostorg/math/commit/66b362aec58b252f179546a0a77fdb6541f1469d?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) to head [(`9ed738f`)](https://app.codecov.io/gh/boostorg/math/commit/9ed738f259302c5bc9fc869f8294b858dcbcf64a?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> Report is 3 commits behind head on develop.  
  
| [Files with missing lines](https://app.codecov.io/gh/boostorg/math/pull/1187?dropdown=coverage&src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Patch % | Lines |  
|---|---|---|  
| [include/boost/math/special\_functions/gamma.hpp](https://app.codecov.io/gh/boostorg/math/pull/1187?src=pr&el=tree&filepath=include%2Fboost%2Fmath%2Fspecial_functions%2Fgamma.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9tYXRoL3NwZWNpYWxfZnVuY3Rpb25zL2dhbW1hLmhwcA==) | 90.62% | [6 Missing :warning: ](https://app.codecov.io/gh/boostorg/math/pull/1187?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) |  
| [include/boost/math/tools/toms748\_solve.hpp](https://app.codecov.io/gh/boostorg/math/pull/1187?src=pr&el=tree&filepath=include%2Fboost%2Fmath%2Ftools%2Ftoms748_solve.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9tYXRoL3Rvb2xzL3RvbXM3NDhfc29sdmUuaHBw) | 89.65% | [3 Missing :warning: ](https://app.codecov.io/gh/boostorg/math/pull/1187?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) |  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/math/pull/1187/graphs/tree.svg?width=650&height=150&src=pr&token=26IDb1OAa7&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/math/pull/1187?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@             Coverage Diff             @@  
##           develop    #1187      +/-   ##  
===========================================  
- Coverage    94.08%   94.06%   -0.02%       
===========================================  
  Files          780      780                
  Lines        65815    65848      +33       
===========================================  
+ Hits         61922    61941      +19       
- Misses        3893     3907      +14       
```  
  
| [Files with missing lines](https://app.codecov.io/gh/boostorg/math/pull/1187?dropdown=coverage&src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/math/distributions/chi\_squared.hpp](https://app.codecov.io/gh/boostorg/math/pull/1187?src=pr&el=tree&filepath=include%2Fboost%2Fmath%2Fdistributions%2Fchi_squared.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9tYXRoL2Rpc3RyaWJ1dGlvbnMvY2hpX3NxdWFyZWQuaHBw) | `90.82% <100.00%> (+0.62%)` | :arrow_up: |  
| [include/boost/math/distributions/weibull.hpp](https://app.codecov.io/gh/boostorg/math/pull/1187?src=pr&el=tree&filepath=include%2Fboost%2Fmath%2Fdistributions%2Fweibull.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9tYXRoL2Rpc3RyaWJ1dGlvbnMvd2VpYnVsbC5ocHA=) | `85.36% <100.00%> (+1.07%)` | :arrow_up: |  
| [include/boost/math/special\_functions/beta.hpp](https://app.codecov.io/gh/boostorg/math/pull/1187?src=pr&el=tree&filepath=include%2Fboost%2Fmath%2Fspecial_functions%2Fbeta.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9tYXRoL3NwZWNpYWxfZnVuY3Rpb25zL2JldGEuaHBw) | `100.00% <ø> (ø)` | |  
| [...boost/math/special\_functions/detail/gamma\_inva.hpp](https://app.codecov.io/gh/boostorg/math/pull/1187?src=pr&el=tree&filepath=include%2Fboost%2Fmath%2Fspecial_functions%2Fdetail%2Fgamma_inva.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9tYXRoL3NwZWNpYWxfZnVuY3Rpb25zL2RldGFpbC9nYW1tYV9pbnZhLmhwcA==) | `90.90% <100.00%> (ø)` | |  
| [...t/math/special\_functions/detail/igamma\_inverse.hpp](https://app.codecov.io/gh/boostorg/math/pull/1187?src=pr&el=tree&filepath=include%2Fboost%2Fmath%2Fspecial_functions%2Fdetail%2Figamma_inverse.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9tYXRoL3NwZWNpYWxfZnVuY3Rpb25zL2RldGFpbC9pZ2FtbWFfaW52ZXJzZS5ocHA=) | `96.77% <100.00%> (+0.03%)` | :arrow_up: |  
| [...ost/math/special\_functions/detail/igamma\_large.hpp](https://app.codecov.io/gh/boostorg/math/pull/1187?src=pr&el=tree&filepath=include%2Fboost%2Fmath%2Fspecial_functions%2Fdetail%2Figamma_large.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9tYXRoL3NwZWNpYWxfZnVuY3Rpb25zL2RldGFpbC9pZ2FtbWFfbGFyZ2UuaHBw) | `33.11% <100.00%> (ø)` | |  
| [...ost/math/special\_functions/detail/lgamma\_small.hpp](https://app.codecov.io/gh/boostorg/math/pull/1187?src=pr&el=tree&filepath=include%2Fboost%2Fmath%2Fspecial_functions%2Fdetail%2Flgamma_small.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9tYXRoL3NwZWNpYWxfZnVuY3Rpb25zL2RldGFpbC9sZ2FtbWFfc21hbGwuaHBw) | `59.90% <100.00%> (ø)` | |  
| [include/boost/math/special\_functions/log1p.hpp](https://app.codecov.io/gh/boostorg/math/pull/1187?src=pr&el=tree&filepath=include%2Fboost%2Fmath%2Fspecial_functions%2Flog1p.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9tYXRoL3NwZWNpYWxfZnVuY3Rpb25zL2xvZzFwLmhwcA==) | `80.59% <100.00%> (ø)` | |  
| [include/boost/math/tools/fraction.hpp](https://app.codecov.io/gh/boostorg/math/pull/1187?src=pr&el=tree&filepath=include%2Fboost%2Fmath%2Ftools%2Ffraction.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9tYXRoL3Rvb2xzL2ZyYWN0aW9uLmhwcA==) | `91.04% <100.00%> (ø)` | |  
| [include/boost/math/tools/precision.hpp](https://app.codecov.io/gh/boostorg/math/pull/1187?src=pr&el=tree&filepath=include%2Fboost%2Fmath%2Ftools%2Fprecision.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9tYXRoL3Rvb2xzL3ByZWNpc2lvbi5ocHA=) | `100.00% <100.00%> (ø)` | |  
| ... and [18 more](https://app.codecov.io/gh/boostorg/math/pull/1187?src=pr&el=tree-more&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | |  
  
... and [1 file with indirect coverage changes](https://app.codecov.io/gh/boostorg/math/pull/1187/indirect-changes?src=pr&el=tree-more&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/math/pull/1187?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/math/pull/1187?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [66b362a...9ed738f](https://app.codecov.io/gh/boostorg/math/pull/1187?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---
