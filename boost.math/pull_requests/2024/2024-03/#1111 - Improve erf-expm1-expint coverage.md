# #1111 Improve erf/expm1/expint coverage. [Merged]

> Username: jzmaddock  
> Created at: 2024-03-06 18:48:15 UTC  
> Updated at: 2025-04-26 08:51:19 UTC  
> Merged at: 2025-04-26 08:51:11 UTC  
> Closed at: 2025-04-26 08:51:11 UTC  
> Url: https://github.com/boostorg/math/pull/1111  

In the expm1 case, tighten up error handling and testing.

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2024-03-11 12:01:57 UTC  
> Updated_at: 2025-04-26 08:51:18 UTC  
> Url: https://github.com/boostorg/math/pull/1111#issuecomment-1988281336  

## [Codecov](https://app.codecov.io/gh/boostorg/math/pull/1111?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
Attention: Patch coverage is `98.25328%` with `4 lines` in your changes missing coverage. Please review.  
> Project coverage is 93.90%. Comparing base [(`01dbedc`)](https://app.codecov.io/gh/boostorg/math/commit/01dbedcb0a0b351713e8e34f00397e04d2f2b0d4?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) to head [(`3e3ac03`)](https://app.codecov.io/gh/boostorg/math/commit/3e3ac037c7c38db466528f04c70fe20566fd7d7a?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> Report is 58 commits behind head on develop.  
  
| [Files with missing lines](https://app.codecov.io/gh/boostorg/math/pull/1111?dropdown=coverage&src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Patch % | Lines |  
|---|---|---|  
| [...ial\_functions/detail/hypergeometric\_1F1\_bessel.hpp](https://app.codecov.io/gh/boostorg/math/pull/1111?src=pr&el=tree&filepath=include%2Fboost%2Fmath%2Fspecial_functions%2Fdetail%2Fhypergeometric_1F1_bessel.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9tYXRoL3NwZWNpYWxfZnVuY3Rpb25zL2RldGFpbC9oeXBlcmdlb21ldHJpY18xRjFfYmVzc2VsLmhwcA==) | 20.00% | [4 Missing :warning: ](https://app.codecov.io/gh/boostorg/math/pull/1111?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) |  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/math/pull/1111/graphs/tree.svg?width=650&height=150&src=pr&token=26IDb1OAa7&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/math/pull/1111?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@             Coverage Diff             @@  
##           develop    #1111      +/-   ##  
===========================================  
+ Coverage    93.84%   93.90%   +0.05%       
===========================================  
  Files          659      661       +2       
  Lines        55313    54559     -754       
===========================================  
- Hits         51909    51233     -676       
+ Misses        3404     3326      -78       
```  
  
| [Files with missing lines](https://app.codecov.io/gh/boostorg/math/pull/1111?dropdown=coverage&src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [.../boost/math/special\_functions/detail/bessel\_ik.hpp](https://app.codecov.io/gh/boostorg/math/pull/1111?src=pr&el=tree&filepath=include%2Fboost%2Fmath%2Fspecial_functions%2Fdetail%2Fbessel_ik.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9tYXRoL3NwZWNpYWxfZnVuY3Rpb25zL2RldGFpbC9iZXNzZWxfaWsuaHBw) | `99.44% <100.00%> (+<0.01%)` | :arrow_up: |  
| [.../boost/math/special\_functions/detail/bessel\_j1.hpp](https://app.codecov.io/gh/boostorg/math/pull/1111?src=pr&el=tree&filepath=include%2Fboost%2Fmath%2Fspecial_functions%2Fdetail%2Fbessel_j1.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9tYXRoL3NwZWNpYWxfZnVuY3Rpb25zL2RldGFpbC9iZXNzZWxfajEuaHBw) | `100.00% <ø> (ø)` | |  
| [.../boost/math/special\_functions/detail/bessel\_k0.hpp](https://app.codecov.io/gh/boostorg/math/pull/1111?src=pr&el=tree&filepath=include%2Fboost%2Fmath%2Fspecial_functions%2Fdetail%2Fbessel_k0.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9tYXRoL3NwZWNpYWxfZnVuY3Rpb25zL2RldGFpbC9iZXNzZWxfazAuaHBw) | `100.00% <ø> (ø)` | |  
| [.../boost/math/special\_functions/detail/bessel\_k1.hpp](https://app.codecov.io/gh/boostorg/math/pull/1111?src=pr&el=tree&filepath=include%2Fboost%2Fmath%2Fspecial_functions%2Fdetail%2Fbessel_k1.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9tYXRoL3NwZWNpYWxfZnVuY3Rpb25zL2RldGFpbC9iZXNzZWxfazEuaHBw) | `100.00% <ø> (ø)` | |  
| [...ial\_functions/detail/hypergeometric\_0F1\_bessel.hpp](https://app.codecov.io/gh/boostorg/math/pull/1111?src=pr&el=tree&filepath=include%2Fboost%2Fmath%2Fspecial_functions%2Fdetail%2Fhypergeometric_0F1_bessel.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9tYXRoL3NwZWNpYWxfZnVuY3Rpb25zL2RldGFpbC9oeXBlcmdlb21ldHJpY18wRjFfYmVzc2VsLmhwcA==) | `100.00% <100.00%> (+42.85%)` | :arrow_up: |  
| [...ost/math/special\_functions/detail/igamma\_large.hpp](https://app.codecov.io/gh/boostorg/math/pull/1111?src=pr&el=tree&filepath=include%2Fboost%2Fmath%2Fspecial_functions%2Fdetail%2Figamma_large.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9tYXRoL3NwZWNpYWxfZnVuY3Rpb25zL2RldGFpbC9pZ2FtbWFfbGFyZ2UuaHBw) | `100.00% <ø> (+2.27%)` | :arrow_up: |  
| [...ost/math/special\_functions/detail/lgamma\_small.hpp](https://app.codecov.io/gh/boostorg/math/pull/1111?src=pr&el=tree&filepath=include%2Fboost%2Fmath%2Fspecial_functions%2Fdetail%2Flgamma_small.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9tYXRoL3NwZWNpYWxfZnVuY3Rpb25zL2RldGFpbC9sZ2FtbWFfc21hbGwuaHBw) | `100.00% <100.00%> (+10.97%)` | :arrow_up: |  
| [.../boost/math/special\_functions/detail/polygamma.hpp](https://app.codecov.io/gh/boostorg/math/pull/1111?src=pr&el=tree&filepath=include%2Fboost%2Fmath%2Fspecial_functions%2Fdetail%2Fpolygamma.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9tYXRoL3NwZWNpYWxfZnVuY3Rpb25zL2RldGFpbC9wb2x5Z2FtbWEuaHBw) | `91.42% <ø> (-0.67%)` | :arrow_down: |  
| [...h/special\_functions/detail/unchecked\_factorial.hpp](https://app.codecov.io/gh/boostorg/math/pull/1111?src=pr&el=tree&filepath=include%2Fboost%2Fmath%2Fspecial_functions%2Fdetail%2Funchecked_factorial.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9tYXRoL3NwZWNpYWxfZnVuY3Rpb25zL2RldGFpbC91bmNoZWNrZWRfZmFjdG9yaWFsLmhwcA==) | `8.92% <ø> (-91.08%)` | :arrow_down: |  
| [include/boost/math/special\_functions/digamma.hpp](https://app.codecov.io/gh/boostorg/math/pull/1111?src=pr&el=tree&filepath=include%2Fboost%2Fmath%2Fspecial_functions%2Fdigamma.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9tYXRoL3NwZWNpYWxfZnVuY3Rpb25zL2RpZ2FtbWEuaHBw) | `100.00% <100.00%> (+18.60%)` | :arrow_up: |  
| ... and [42 more](https://app.codecov.io/gh/boostorg/math/pull/1111?src=pr&el=tree-more&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | |  
  
... and [16 files with indirect coverage changes](https://app.codecov.io/gh/boostorg/math/pull/1111/indirect-changes?src=pr&el=tree-more&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/math/pull/1111?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/math/pull/1111?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [01dbedc...3e3ac03](https://app.codecov.io/gh/boostorg/math/pull/1111?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>  
<details><summary> :rocket: New features to boost your workflow: </summary>  
  
- :snowflake: [Test Analytics](https://docs.codecov.com/docs/test-analytics): Detect flaky tests, report on failures, and find test suite problems.  
</details>

---

## Comment 2

> Username: jzmaddock  
> Created_at: 2025-04-23 11:13:57 UTC  
> Url: https://github.com/boostorg/math/pull/1111#issuecomment-2823941256  

@mborland @ckormanyos @NAThompson This PR is now good to go and completes code coverage improvements as far as G for gamma.hpp.  There are a lot of accumulated changes here, so I'll hold off for a few days at least in case there are any comments...  
  
Also not sure what's happened to the drone CI runner: it seems to be terminally broken right now?

---

## Comment 3

> Username: mborland  
> Created_at: 2025-04-23 14:27:06 UTC  
> Url: https://github.com/boostorg/math/pull/1111#issuecomment-2824505121  

@sdarwin have something gone sideways with our drone config? It looks like we have quite a few runs over the past several days that aren't picking up? There are some like this that seem to have completely crashed before the retry step in the clone phase: https://drone.cpp.al/boostorg/math/2225/193/1

---

## Review 4 [Commented]

> Username: mborland  
> Created_at: 2025-04-23 14:45:11 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/1111#pullrequestreview-2787615405  

This all looks good to me John. Once it's in I can run it through the actual CUDA testing to see if there's any changes that need to be made there. I think the only thing I saw with the replacement of `BOOST_MATH_FP_XXX` with the standard `FP_XXX` macros. No matter since Scipy is now pinned against specific versions instead of tracking develop.

---

## Comment 5

> Username: jzmaddock  
> Created_at: 2025-04-23 14:56:09 UTC  
> Url: https://github.com/boostorg/math/pull/1111#issuecomment-2824596135  

>This all looks good to me John. Once it's in I can run it through the actual CUDA testing to see if there's any changes that need to be made there. I think the only thing I saw with the replacement of BOOST_MATH_FP_XXX with the standard FP_XXX macros. No matter since Scipy is now pinned against specific versions instead of tracking develop.  
  
Where was this?  I didn't think I'd touched tools/config.hpp?

---

## Review 6 [Commented]

> Username: mborland  
> Created_at: 2025-04-23 15:02:34 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/1111#pullrequestreview-2787676210  

📁 include/boost/math/special_functions/gamma.hpp

```diff
 952 |-    if((boost::math::fpclassify)(prefix) == (int)BOOST_MATH_FP_INFINITE)
 953 |-       return policies::raise_overflow_error<T>("boost::math::detail::full_igamma_prefix<%1%>(%1%, %1%)", "Result of incomplete gamma function is too large to represent.", pol);
 982 |+    if((boost::math::fpclassify)(prefix) == (int)FP_INFINITE)
```

> Username: mborland  
> Created_at: 2025-04-23 15:02:33 UTC  
> Updated_at: 2025-04-23 15:02:34 UTC  
> Url: https://github.com/boostorg/math/pull/1111#discussion_r2056266770  

```diff  
-   if((boost::math::fpclassify)(prefix) == (int)FP_INFINITE)  
+   if((boost::math::fpclassify)(prefix) == (int)BOOST_MATH_FP_INFINITE)  
```

> Username: jzmaddock  
> Created_at: 2025-04-23 15:07:28 UTC  
> Url: https://github.com/boostorg/math/pull/1111#discussion_r2056277829  

Good catch!  
  
I'll do that before merging.  
  
Unrelated question: should BOOST_MATH_FP_* map to the std values of FP_* ?

> Username: mborland  
> Created_at: 2025-04-23 15:18:59 UTC  
> Url: https://github.com/boostorg/math/pull/1111#discussion_r2056299020  

They do: https://github.com/boostorg/math/blob/develop/include/boost/math/tools/config.hpp#L780-L784, except in the case of NVRTC where they map to the conventional values: https://github.com/boostorg/math/blob/develop/include/boost/math/tools/config.hpp#L830-L834, which works since `boost::math::fpclassify` returns the `BOOST_MATH_XXX` macro values

> Username: jzmaddock  
> Created_at: 2025-04-24 08:26:21 UTC  
> Url: https://github.com/boostorg/math/pull/1111#discussion_r2057840891  

Doh!  My bad, I mis-read my grep output.  Thanks Matt.


---

## Comment 7

> Username: jzmaddock  
> Created_at: 2025-04-26 08:51:03 UTC  
> Url: https://github.com/boostorg/math/pull/1111#issuecomment-2831970422  

OK, drone is back up and running again, just the few known about sycl tests failing, so merging...

---
