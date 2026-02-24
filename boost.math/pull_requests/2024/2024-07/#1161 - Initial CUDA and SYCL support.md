# #1161 Initial CUDA and SYCL support [Merged]

> Username: mborland  
> Created at: 2024-07-23 12:59:53 UTC  
> Updated at: 2024-07-24 15:53:22 UTC  
> Merged at: 2024-07-24 15:53:19 UTC  
> Closed at: 2024-07-24 15:53:19 UTC  
> Url: https://github.com/boostorg/math/pull/1161  

Adds CUDA support for binomial distribution  
Adds CUDA and SYCL support for beta function and arcsine distribution.  
  
 This is just the changes to the include directory cherry picked from: https://github.com/cppalliance/cuda-math/pull/2.

---

## Review 1 [Commented]

> Username: jzmaddock  
> Created_at: 2024-07-23 15:15:52 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/1161#pullrequestreview-2194297280  

📁 include/boost/math/special_functions/beta.hpp

```diff
  37 |    BOOST_MATH_STD_USING  // for ADL of std names
  38 | 
  39 |+    #ifndef BOOST_MATH_NO_EXCEPTIONS
```

> Username: jzmaddock  
> Created_at: 2024-07-23 15:15:52 UTC  
> Url: https://github.com/boostorg/math/pull/1161#discussion_r1688250971  

Shouldn't we keep this block, even with no exceptions, and let the error handlers return NaN?  Otherwise for invalid arguments, pretty much anything could happen, including infinite loops or whatever...


---

## Review 2 [Commented]

> Username: jzmaddock  
> Created_at: 2024-07-23 15:19:33 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/1161#pullrequestreview-2194307952  

📁 include/boost/math/policies/error_handling.hpp

```diff
 382 |            const  ::boost::math::policies::overflow_error< ::boost::math::policies::errno_on_error>&) BOOST_MATH_NOEXCEPT(T)
 383 | {
 384 |+    #ifndef BOOST_MATH_SYCL_ENABLED
```

> Username: jzmaddock  
> Created_at: 2024-07-23 15:19:33 UTC  
> Url: https://github.com/boostorg/math/pull/1161#discussion_r1688257683  

Rather than #if'ing this out, I would just make sure that the default error policy was `ignore_error` for all the error types when building for the device.  Same with the other #if blocks below?

> Username: mborland  
> Created_at: 2024-07-23 15:41:12 UTC  
> Url: https://github.com/boostorg/math/pull/1161#discussion_r1688292168  

I was thinking adding a GPU default policy of:  
  
```  
using gpu_policy = policy<  
      promote_float<false>,  
      promote_double<false>,  
      overflow_error<ignore_error>  
      >;  
```  
  
I guess this could also just overwrite the value of `policies::policy<>()` when `BOOST_MATH_HAS_GPU_SUPPORT` is defined?

> Username: jzmaddock  
> Created_at: 2024-07-24 15:41:33 UTC  
> Url: https://github.com/boostorg/math/pull/1161#discussion_r1690042714  

Right, when building for the device, set the default error handling to ignore_error for everything, ie:  
  
```  
#    define BOOST_MATH_DOMAIN_ERROR_POLICY ignore_error  
#     define BOOST_MATH_POLE_ERROR_POLICY ignore_error  
#     define BOOST_MATH_OVERFLOW_ERROR_POLICY ignore_error  
#     define BOOST_MATH_EVALUATION_ERROR_POLICY ignore_error  
#     define BOOST_MATH_ROUNDING_ERROR_POLICY ignore_error  
```  
  
Currently the default when exceptions are disabled is `errno_on_error`, but we have no access to `::errno` so don't want that here.


---

## Review 3 [Commented]

> Username: jzmaddock  
> Created_at: 2024-07-23 15:21:05 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/1161#pullrequestreview-2194311688  

📁 include/boost/math/special_functions/beta.hpp

```diff
 917 | {
 918 |    static constexpr unsigned value = 15; // ~8-15 digit accuracy
 919 |+ #ifndef BOOST_MATH_HAS_GPU_SUPPORT
```

> Username: jzmaddock  
> Created_at: 2024-07-23 15:21:05 UTC  
> Url: https://github.com/boostorg/math/pull/1161#discussion_r1688259993  

Do we still have no static_assert?  That's a shame :(

> Username: mborland  
> Created_at: 2024-07-23 15:53:32 UTC  
> Updated_at: 2024-07-23 15:53:33 UTC  
> Url: https://github.com/boostorg/math/pull/1161#discussion_r1688311676  

The `static_assert` should be fine, but the issue here is `operator[]` on std::array can't be used.

> Username: jzmaddock  
> Created_at: 2024-07-24 15:33:36 UTC  
> Url: https://github.com/boostorg/math/pull/1161#discussion_r1690030005  

Doh!


---

## Review 4 [Commented]

> Username: jzmaddock  
> Created_at: 2024-07-23 15:26:13 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/1161#pullrequestreview-2194324432  

📁 include/boost/math/tools/polynomial.hpp

```diff
 420 | 
 421 |-    polynomial<T> integrate() const
 421 |+    BOOST_MATH_GPU_ENABLED polynomial<T> integrate() const
```

> Username: jzmaddock  
> Created_at: 2024-07-23 15:26:13 UTC  
> Url: https://github.com/boostorg/math/pull/1161#discussion_r1688268183  

Question: can we actually use this on a device - with std::vector?

> Username: mborland  
> Created_at: 2024-07-23 15:38:08 UTC  
> Url: https://github.com/boostorg/math/pull/1161#discussion_r1688286745  

Looks like no: https://github.com/NVIDIA/cccl/discussions/1032


---

## Comment 5

> Username: jzmaddock  
> Created_at: 2024-07-23 15:29:48 UTC  
> Url: https://github.com/boostorg/math/pull/1161#issuecomment-2245566389  

Thanks Matt - this is looking really cool!  
  
I guess testing wise, my main worry is that we see a random PR go green and merging it accidentally breaks CUDA/Sycl builds because something isn't correctly marked up.  If you're able to test building and linking for CUDA (even if not running) on a regular Github runner that would be amazing!

---

## Comment 6

> Username: mborland  
> Created_at: 2024-07-23 20:07:29 UTC  
> Url: https://github.com/boostorg/math/pull/1161#issuecomment-2246220363  

@jzmaddock I added a complete SYCL run, and just the compilation step for CUDA like you have for Config.

---

## Comment 7

> Username: codecov[bot]  
> Created_at: 2024-07-23 21:24:52 UTC  
> Updated_at: 2024-07-24 02:36:46 UTC  
> Url: https://github.com/boostorg/math/pull/1161#issuecomment-2246345229  

## [Codecov](https://app.codecov.io/gh/boostorg/math/pull/1161?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
Attention: Patch coverage is `97.76786%` with `10 lines` in your changes missing coverage. Please review.  
> Project coverage is 91.50%. Comparing base [(`07d97df`)](https://app.codecov.io/gh/boostorg/math/commit/07d97df50ceb04bafbeb29182dcaaf5c4b1163b6?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) to head [(`f17946b`)](https://app.codecov.io/gh/boostorg/math/commit/f17946b74bc5b696cf747cb63a1d999c55c2e51a?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/math/pull/1161/graphs/tree.svg?width=650&height=150&src=pr&token=26IDb1OAa7&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/math/pull/1161?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@             Coverage Diff             @@  
##           develop    #1161      +/-   ##  
===========================================  
+ Coverage    91.48%   91.50%   +0.02%       
===========================================  
  Files          774      774                
  Lines        64303    64318      +15       
===========================================  
+ Hits         58827    58854      +27       
+ Misses        5476     5464      -12       
```  
  
| [Files](https://app.codecov.io/gh/boostorg/math/pull/1161?dropdown=coverage&src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/math/constants/constants.hpp](https://app.codecov.io/gh/boostorg/math/pull/1161?src=pr&el=tree&filepath=include%2Fboost%2Fmath%2Fconstants%2Fconstants.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9tYXRoL2NvbnN0YW50cy9jb25zdGFudHMuaHBw) | `100.00% <100.00%> (ø)` | |  
| [include/boost/math/distributions/arcsine.hpp](https://app.codecov.io/gh/boostorg/math/pull/1161?src=pr&el=tree&filepath=include%2Fboost%2Fmath%2Fdistributions%2Farcsine.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9tYXRoL2Rpc3RyaWJ1dGlvbnMvYXJjc2luZS5ocHA=) | `91.32% <100.00%> (+0.25%)` | :arrow_up: |  
| [include/boost/math/distributions/binomial.hpp](https://app.codecov.io/gh/boostorg/math/pull/1161?src=pr&el=tree&filepath=include%2Fboost%2Fmath%2Fdistributions%2Fbinomial.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9tYXRoL2Rpc3RyaWJ1dGlvbnMvYmlub21pYWwuaHBw) | `86.56% <100.00%> (ø)` | |  
| [include/boost/math/special\_functions/beta.hpp](https://app.codecov.io/gh/boostorg/math/pull/1161?src=pr&el=tree&filepath=include%2Fboost%2Fmath%2Fspecial_functions%2Fbeta.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9tYXRoL3NwZWNpYWxfZnVuY3Rpb25zL2JldGEuaHBw) | `100.00% <100.00%> (ø)` | |  
| [...st/math/special\_functions/detail/ibeta\_inverse.hpp](https://app.codecov.io/gh/boostorg/math/pull/1161?src=pr&el=tree&filepath=include%2Fboost%2Fmath%2Fspecial_functions%2Fdetail%2Fibeta_inverse.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9tYXRoL3NwZWNpYWxfZnVuY3Rpb25zL2RldGFpbC9pYmV0YV9pbnZlcnNlLmhwcA==) | `92.23% <100.00%> (+0.03%)` | :arrow_up: |  
| [...nclude/boost/math/special\_functions/fpclassify.hpp](https://app.codecov.io/gh/boostorg/math/pull/1161?src=pr&el=tree&filepath=include%2Fboost%2Fmath%2Fspecial_functions%2Ffpclassify.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9tYXRoL3NwZWNpYWxfZnVuY3Rpb25zL2ZwY2xhc3NpZnkuaHBw) | `98.68% <ø> (ø)` | |  
| [include/boost/math/special\_functions/log1p.hpp](https://app.codecov.io/gh/boostorg/math/pull/1161?src=pr&el=tree&filepath=include%2Fboost%2Fmath%2Fspecial_functions%2Flog1p.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9tYXRoL3NwZWNpYWxfZnVuY3Rpb25zL2xvZzFwLmhwcA==) | `80.30% <100.00%> (+0.30%)` | :arrow_up: |  
| [include/boost/math/tools/big\_constant.hpp](https://app.codecov.io/gh/boostorg/math/pull/1161?src=pr&el=tree&filepath=include%2Fboost%2Fmath%2Ftools%2Fbig_constant.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9tYXRoL3Rvb2xzL2JpZ19jb25zdGFudC5ocHA=) | `100.00% <100.00%> (ø)` | |  
| [.../boost/math/tools/detail/polynomial\_horner3\_20.hpp](https://app.codecov.io/gh/boostorg/math/pull/1161?src=pr&el=tree&filepath=include%2Fboost%2Fmath%2Ftools%2Fdetail%2Fpolynomial_horner3_20.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9tYXRoL3Rvb2xzL2RldGFpbC9wb2x5bm9taWFsX2hvcm5lcjNfMjAuaHBw) | `100.00% <100.00%> (ø)` | |  
| [...de/boost/math/tools/detail/rational\_horner3\_20.hpp](https://app.codecov.io/gh/boostorg/math/pull/1161?src=pr&el=tree&filepath=include%2Fboost%2Fmath%2Ftools%2Fdetail%2Frational_horner3_20.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9tYXRoL3Rvb2xzL2RldGFpbC9yYXRpb25hbF9ob3JuZXIzXzIwLmhwcA==) | `97.66% <100.00%> (ø)` | |  
| ... and [13 more](https://app.codecov.io/gh/boostorg/math/pull/1161?src=pr&el=tree-more&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | |  
  
... and [1 file with indirect coverage changes](https://app.codecov.io/gh/boostorg/math/pull/1161/indirect-changes?src=pr&el=tree-more&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/math/pull/1161?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/math/pull/1161?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [07d97df...f17946b](https://app.codecov.io/gh/boostorg/math/pull/1161?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---

## Comment 8

> Username: mborland  
> Created_at: 2024-07-23 21:32:29 UTC  
> Url: https://github.com/boostorg/math/pull/1161#issuecomment-2246355084  

Pending any other questions or comments I think this initial push is good to merge. I will continue to work on this in https://github.com/cppalliance/cuda-math and then port things over once they're tested

---

## Comment 9

> Username: jzmaddock  
> Created_at: 2024-07-24 15:42:16 UTC  
> Url: https://github.com/boostorg/math/pull/1161#issuecomment-2248335627  

> Pending any other questions or comments I think this initial push is good to merge.  
  
Go for it when you're ready! :)

---
