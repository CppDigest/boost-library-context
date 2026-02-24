# #1271 Fix GPU support [Merged]

> Username: mborland  
> Created at: 2025-06-10 16:32:22 UTC  
> Updated at: 2025-06-11 13:12:34 UTC  
> Merged at: 2025-06-11 13:12:26 UTC  
> Closed at: 2025-06-11 13:12:26 UTC  
> Url: https://github.com/boostorg/math/pull/1271  

Fixes up support for all our GPU devices. I have no idea why the changes to ibeta and ibeta_inv fail with SYCL, they still run totally fine on NVCC and NVRTC so it's not a lack of compatibility. They are pretty far off in some cases too:  
  
```  
/home/mborland/Documents/boost/libs/math/test/../include_private/boost/math/tools/test.hpp(248): error: in "test_main": Gross error  
ibeta<float> Max = 5.105e+06 RMS Mean=5.487e+05  
    worst case at row: 1039  
    { 1e+16, 1.0043945e+16, 0.49890375, 0, 0, 2.1688104e-19, 1 }  
Peak error greater than expected value of 2  
  
```

---

## Comment 1

> Username: mborland  
> Created_at: 2025-06-10 17:29:57 UTC  
> Url: https://github.com/boostorg/math/pull/1271#issuecomment-2960081407  

I forgot that Windows-2019 and Ubuntu 22.04 are deprecated now and need removed. I'll open a second PR for those that'll get merged into this one.

---

## Comment 2

> Username: codecov[bot]  
> Created_at: 2025-06-10 20:02:30 UTC  
> Url: https://github.com/boostorg/math/pull/1271#issuecomment-2960445033  

## [Codecov](https://app.codecov.io/gh/boostorg/math/pull/1271?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
All modified and coverable lines are covered by tests :white_check_mark:  
> Project coverage is 95.07%. Comparing base [(`2b05d87`)](https://app.codecov.io/gh/boostorg/math/commit/2b05d87c0f62d873bb160873ac130e3dc4428079?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) to head [(`54c04dd`)](https://app.codecov.io/gh/boostorg/math/commit/54c04dd4df45617f07acade2a2eead9345689a05?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/math/pull/1271/graphs/tree.svg?width=650&height=150&src=pr&token=26IDb1OAa7&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/math/pull/1271?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@           Coverage Diff            @@  
##           develop    #1271   +/-   ##  
========================================  
  Coverage    95.07%   95.07%             
========================================  
  Files          792      792             
  Lines        66882    66882             
========================================  
  Hits         63589    63589             
  Misses        3293     3293             
```  
  
| [Files with missing lines](https://app.codecov.io/gh/boostorg/math/pull/1271?dropdown=coverage&src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/math/distributions/arcsine.hpp](https://app.codecov.io/gh/boostorg/math/pull/1271?src=pr&el=tree&filepath=include%2Fboost%2Fmath%2Fdistributions%2Farcsine.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9tYXRoL2Rpc3RyaWJ1dGlvbnMvYXJjc2luZS5ocHA=) | `91.37% <ø> (ø)` | |  
| [include/boost/math/distributions/bernoulli.hpp](https://app.codecov.io/gh/boostorg/math/pull/1271?src=pr&el=tree&filepath=include%2Fboost%2Fmath%2Fdistributions%2Fbernoulli.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9tYXRoL2Rpc3RyaWJ1dGlvbnMvYmVybm91bGxpLmhwcA==) | `88.88% <ø> (ø)` | |  
| [include/boost/math/distributions/cauchy.hpp](https://app.codecov.io/gh/boostorg/math/pull/1271?src=pr&el=tree&filepath=include%2Fboost%2Fmath%2Fdistributions%2Fcauchy.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9tYXRoL2Rpc3RyaWJ1dGlvbnMvY2F1Y2h5LmhwcA==) | `83.75% <ø> (ø)` | |  
| [include/boost/math/distributions/normal.hpp](https://app.codecov.io/gh/boostorg/math/pull/1271?src=pr&el=tree&filepath=include%2Fboost%2Fmath%2Fdistributions%2Fnormal.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9tYXRoL2Rpc3RyaWJ1dGlvbnMvbm9ybWFsLmhwcA==) | `86.13% <ø> (ø)` | |  
| [include/boost/math/distributions/triangular.hpp](https://app.codecov.io/gh/boostorg/math/pull/1271?src=pr&el=tree&filepath=include%2Fboost%2Fmath%2Fdistributions%2Ftriangular.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9tYXRoL2Rpc3RyaWJ1dGlvbnMvdHJpYW5ndWxhci5ocHA=) | `89.09% <ø> (ø)` | |  
| [include/boost/math/distributions/uniform.hpp](https://app.codecov.io/gh/boostorg/math/pull/1271?src=pr&el=tree&filepath=include%2Fboost%2Fmath%2Fdistributions%2Funiform.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9tYXRoL2Rpc3RyaWJ1dGlvbnMvdW5pZm9ybS5ocHA=) | `84.93% <ø> (ø)` | |  
| [include/boost/math/policies/error\_handling.hpp](https://app.codecov.io/gh/boostorg/math/pull/1271?src=pr&el=tree&filepath=include%2Fboost%2Fmath%2Fpolicies%2Ferror_handling.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9tYXRoL3BvbGljaWVzL2Vycm9yX2hhbmRsaW5nLmhwcA==) | `85.58% <ø> (ø)` | |  
| [include/boost/math/special\_functions/beta.hpp](https://app.codecov.io/gh/boostorg/math/pull/1271?src=pr&el=tree&filepath=include%2Fboost%2Fmath%2Fspecial_functions%2Fbeta.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9tYXRoL3NwZWNpYWxfZnVuY3Rpb25zL2JldGEuaHBw) | `100.00% <100.00%> (ø)` | |  
| [.../boost/math/special\_functions/detail/bessel\_i1.hpp](https://app.codecov.io/gh/boostorg/math/pull/1271?src=pr&el=tree&filepath=include%2Fboost%2Fmath%2Fspecial_functions%2Fdetail%2Fbessel_i1.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9tYXRoL3NwZWNpYWxfZnVuY3Rpb25zL2RldGFpbC9iZXNzZWxfaTEuaHBw) | `100.00% <100.00%> (ø)` | |  
| [.../boost/math/special\_functions/detail/bessel\_k1.hpp](https://app.codecov.io/gh/boostorg/math/pull/1271?src=pr&el=tree&filepath=include%2Fboost%2Fmath%2Fspecial_functions%2Fdetail%2Fbessel_k1.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9tYXRoL3NwZWNpYWxfZnVuY3Rpb25zL2RldGFpbC9iZXNzZWxfazEuaHBw) | `100.00% <100.00%> (ø)` | |  
| ... and [48 more](https://app.codecov.io/gh/boostorg/math/pull/1271?src=pr&el=tree-more&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | |  
  
... and [14 files with indirect coverage changes](https://app.codecov.io/gh/boostorg/math/pull/1271/indirect-changes?src=pr&el=tree-more&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/math/pull/1271?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/math/pull/1271?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [2b05d87...54c04dd](https://app.codecov.io/gh/boostorg/math/pull/1271?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>  
<details><summary> :rocket: New features to boost your workflow: </summary>  
  
- :snowflake: [Test Analytics](https://docs.codecov.com/docs/test-analytics): Detect flaky tests, report on failures, and find test suite problems.  
</details>

---

## Comment 3

> Username: mborland  
> Created_at: 2025-06-10 20:17:48 UTC  
> Url: https://github.com/boostorg/math/pull/1271#issuecomment-2960480796  

Closes: https://github.com/boostorg/multiprecision/issues/695

---

## Comment 4

> Username: mborland  
> Created_at: 2025-06-11 13:11:59 UTC  
> Url: https://github.com/boostorg/math/pull/1271#issuecomment-2962659011  

I believe this gets us back to green. The drone errors are all the regular clone/connection problems.

---
