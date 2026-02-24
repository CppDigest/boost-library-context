# #1184 GPU Batch 6 [Merged]

> Username: mborland  
> Created at: 2024-08-20 21:00:26 UTC  
> Updated at: 2024-08-22 14:06:30 UTC  
> Merged at: 2024-08-22 14:05:38 UTC  
> Closed at: 2024-08-22 14:05:38 UTC  
> Url: https://github.com/boostorg/math/pull/1184  

Adds unified GPU support for the bessel functions: `cyl_bessel_j`, `sph_bessel`, `sph_bessel_j`, `cyl_bessel_i`, `cyl_bessel_k`, `cyl_neumann`, and `sph_neumann` with all the associated detail functions (e.g. `bessel_i0`, `bessel_i1`, etc).  
  
Adds an array header to detect when to use `cuda::std::array` or `std::array` to support the use of factorials. Also adds preliminary support for Newton Rhapson on device, but @NAThompson and I think it can be improved.   
  
Completed CI runs on device can be found here: https://github.com/cppalliance/cuda-math/pull/16  
  
CC: @steppi, @izaid, and @dschmitz89

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2024-08-20 22:05:01 UTC  
> Updated_at: 2024-08-22 14:06:30 UTC  
> Url: https://github.com/boostorg/math/pull/1184#issuecomment-2299841674  

## [Codecov](https://app.codecov.io/gh/boostorg/math/pull/1184?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
Attention: Patch coverage is `97.51037%` with `6 lines` in your changes missing coverage. Please review.  
> Project coverage is 94.06%. Comparing base [(`1d40454`)](https://app.codecov.io/gh/boostorg/math/commit/1d40454024feae575357aa9d19fdf5756ec20b56?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) to head [(`047c206`)](https://app.codecov.io/gh/boostorg/math/commit/047c206c3028331d353e20a13855f8fafb0ccd5e?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> Report is 2 commits behind head on develop.  
  
| [Files](https://app.codecov.io/gh/boostorg/math/pull/1184?dropdown=coverage&src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Patch % | Lines |  
|---|---|---|  
| [include/boost/math/special\_functions/expm1.hpp](https://app.codecov.io/gh/boostorg/math/pull/1184?src=pr&el=tree&filepath=include%2Fboost%2Fmath%2Fspecial_functions%2Fexpm1.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9tYXRoL3NwZWNpYWxfZnVuY3Rpb25zL2V4cG0xLmhwcA==) | 83.33% | [3 Missing :warning: ](https://app.codecov.io/gh/boostorg/math/pull/1184?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) |  
| [include/boost/math/tools/roots.hpp](https://app.codecov.io/gh/boostorg/math/pull/1184?src=pr&el=tree&filepath=include%2Fboost%2Fmath%2Ftools%2Froots.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9tYXRoL3Rvb2xzL3Jvb3RzLmhwcA==) | 84.21% | [3 Missing :warning: ](https://app.codecov.io/gh/boostorg/math/pull/1184?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) |  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/math/pull/1184/graphs/tree.svg?width=650&height=150&src=pr&token=26IDb1OAa7&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/math/pull/1184?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@           Coverage Diff            @@  
##           develop    #1184   +/-   ##  
========================================  
  Coverage    94.06%   94.06%             
========================================  
  Files          780      780             
  Lines        65796    65815   +19       
========================================  
+ Hits         61891    61910   +19       
  Misses        3905     3905             
```  
  
| [Files](https://app.codecov.io/gh/boostorg/math/pull/1184?dropdown=coverage&src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/math/special\_functions/bessel.hpp](https://app.codecov.io/gh/boostorg/math/pull/1184?src=pr&el=tree&filepath=include%2Fboost%2Fmath%2Fspecial_functions%2Fbessel.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9tYXRoL3NwZWNpYWxfZnVuY3Rpb25zL2Jlc3NlbC5ocHA=) | `100.00% <100.00%> (ø)` | |  
| [.../math/special\_functions/detail/airy\_ai\_bi\_zero.hpp](https://app.codecov.io/gh/boostorg/math/pull/1184?src=pr&el=tree&filepath=include%2Fboost%2Fmath%2Fspecial_functions%2Fdetail%2Fairy_ai_bi_zero.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9tYXRoL3NwZWNpYWxfZnVuY3Rpb25zL2RldGFpbC9haXJ5X2FpX2JpX3plcm8uaHBw) | `92.00% <100.00%> (ø)` | |  
| [.../boost/math/special\_functions/detail/bessel\_i0.hpp](https://app.codecov.io/gh/boostorg/math/pull/1184?src=pr&el=tree&filepath=include%2Fboost%2Fmath%2Fspecial_functions%2Fdetail%2Fbessel_i0.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9tYXRoL3NwZWNpYWxfZnVuY3Rpb25zL2RldGFpbC9iZXNzZWxfaTAuaHBw) | `100.00% <100.00%> (ø)` | |  
| [.../boost/math/special\_functions/detail/bessel\_i1.hpp](https://app.codecov.io/gh/boostorg/math/pull/1184?src=pr&el=tree&filepath=include%2Fboost%2Fmath%2Fspecial_functions%2Fdetail%2Fbessel_i1.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9tYXRoL3NwZWNpYWxfZnVuY3Rpb25zL2RldGFpbC9iZXNzZWxfaTEuaHBw) | `100.00% <100.00%> (ø)` | |  
| [.../boost/math/special\_functions/detail/bessel\_ik.hpp](https://app.codecov.io/gh/boostorg/math/pull/1184?src=pr&el=tree&filepath=include%2Fboost%2Fmath%2Fspecial_functions%2Fdetail%2Fbessel_ik.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9tYXRoL3NwZWNpYWxfZnVuY3Rpb25zL2RldGFpbC9iZXNzZWxfaWsuaHBw) | `100.00% <100.00%> (ø)` | |  
| [.../boost/math/special\_functions/detail/bessel\_j0.hpp](https://app.codecov.io/gh/boostorg/math/pull/1184?src=pr&el=tree&filepath=include%2Fboost%2Fmath%2Fspecial_functions%2Fdetail%2Fbessel_j0.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9tYXRoL3NwZWNpYWxfZnVuY3Rpb25zL2RldGFpbC9iZXNzZWxfajAuaHBw) | `100.00% <100.00%> (ø)` | |  
| [.../boost/math/special\_functions/detail/bessel\_j1.hpp](https://app.codecov.io/gh/boostorg/math/pull/1184?src=pr&el=tree&filepath=include%2Fboost%2Fmath%2Fspecial_functions%2Fdetail%2Fbessel_j1.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9tYXRoL3NwZWNpYWxfZnVuY3Rpb25zL2RldGFpbC9iZXNzZWxfajEuaHBw) | `100.00% <100.00%> (ø)` | |  
| [.../boost/math/special\_functions/detail/bessel\_jn.hpp](https://app.codecov.io/gh/boostorg/math/pull/1184?src=pr&el=tree&filepath=include%2Fboost%2Fmath%2Fspecial_functions%2Fdetail%2Fbessel_jn.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9tYXRoL3NwZWNpYWxfZnVuY3Rpb25zL2RldGFpbC9iZXNzZWxfam4uaHBw) | `100.00% <100.00%> (ø)` | |  
| [.../boost/math/special\_functions/detail/bessel\_jy.hpp](https://app.codecov.io/gh/boostorg/math/pull/1184?src=pr&el=tree&filepath=include%2Fboost%2Fmath%2Fspecial_functions%2Fdetail%2Fbessel_jy.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9tYXRoL3NwZWNpYWxfZnVuY3Rpb25zL2RldGFpbC9iZXNzZWxfankuaHBw) | `100.00% <100.00%> (ø)` | |  
| [...t/math/special\_functions/detail/bessel\_jy\_asym.hpp](https://app.codecov.io/gh/boostorg/math/pull/1184?src=pr&el=tree&filepath=include%2Fboost%2Fmath%2Fspecial_functions%2Fdetail%2Fbessel_jy_asym.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9tYXRoL3NwZWNpYWxfZnVuY3Rpb25zL2RldGFpbC9iZXNzZWxfanlfYXN5bS5ocHA=) | `100.00% <100.00%> (ø)` | |  
| ... and [28 more](https://app.codecov.io/gh/boostorg/math/pull/1184?src=pr&el=tree-more&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | |  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/math/pull/1184?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/math/pull/1184?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [1d40454...047c206](https://app.codecov.io/gh/boostorg/math/pull/1184?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---
