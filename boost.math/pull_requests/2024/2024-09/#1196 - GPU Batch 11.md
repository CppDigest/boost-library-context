# #1196 GPU Batch 11 [Merged]

> Username: mborland  
> Created at: 2024-09-12 14:55:02 UTC  
> Updated at: 2024-09-16 12:42:10 UTC  
> Merged at: 2024-09-16 12:41:54 UTC  
> Closed at: 2024-09-16 12:41:55 UTC  
> Url: https://github.com/boostorg/math/pull/1196  

This PR adds two double exponential quadrature routines: `sinh_sinh` and `exp_sinh`. To improve the performance the architecture is different with a `__device__` only function signature which uses pointers to arrays of pre-calculated coefficients that are stored in shared memory on device. These routines are only supported by NVCC and NVRTC.  
  
Completed on device runs can be found: https://github.com/cppalliance/cuda-math/pull/25  
  
CC: @dschmitz89, @izaid, @steppi  
  
Should the three of you be interested in integrating the quadrature routines from Boost.Math into your projects they were all initially written by @NAThompson. He is more than happy to answer questions about them.

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2024-09-12 15:52:20 UTC  
> Updated_at: 2024-09-16 12:42:10 UTC  
> Url: https://github.com/boostorg/math/pull/1196#issuecomment-2346663876  

## [Codecov](https://app.codecov.io/gh/boostorg/math/pull/1196?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
All modified and coverable lines are covered by tests :white_check_mark:  
> Project coverage is 93.80%. Comparing base [(`937107a`)](https://app.codecov.io/gh/boostorg/math/commit/937107ac82368a03287bb6f2227f264377450641?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) to head [(`b5214b5`)](https://app.codecov.io/gh/boostorg/math/commit/b5214b5935bea24021c719eea3451d1eec3024fb?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> Report is 2 commits behind head on develop.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/math/pull/1196/graphs/tree.svg?width=650&height=150&src=pr&token=26IDb1OAa7&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/math/pull/1196?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@           Coverage Diff            @@  
##           develop    #1196   +/-   ##  
========================================  
  Coverage    93.80%   93.80%             
========================================  
  Files          657      657             
  Lines        53881    53881             
========================================  
  Hits         50541    50541             
  Misses        3340     3340             
```  
  
| [Files with missing lines](https://app.codecov.io/gh/boostorg/math/pull/1196?dropdown=coverage&src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [...e/boost/math/quadrature/detail/exp\_sinh\_detail.hpp](https://app.codecov.io/gh/boostorg/math/pull/1196?src=pr&el=tree&filepath=include%2Fboost%2Fmath%2Fquadrature%2Fdetail%2Fexp_sinh_detail.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9tYXRoL3F1YWRyYXR1cmUvZGV0YWlsL2V4cF9zaW5oX2RldGFpbC5ocHA=) | `93.42% <ø> (ø)` | |  
| [include/boost/math/quadrature/exp\_sinh.hpp](https://app.codecov.io/gh/boostorg/math/pull/1196?src=pr&el=tree&filepath=include%2Fboost%2Fmath%2Fquadrature%2Fexp_sinh.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9tYXRoL3F1YWRyYXR1cmUvZXhwX3NpbmguaHBw) | `85.71% <ø> (ø)` | |  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/math/pull/1196?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/math/pull/1196?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [937107a...b5214b5](https://app.codecov.io/gh/boostorg/math/pull/1196?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---
