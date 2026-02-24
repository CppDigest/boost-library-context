# #1167 GPU Batch 5 [Merged]

> Username: mborland  
> Created at: 2024-07-30 20:56:14 UTC  
> Updated at: 2024-08-06 13:01:25 UTC  
> Merged at: 2024-08-06 13:01:22 UTC  
> Closed at: 2024-08-06 13:01:22 UTC  
> Url: https://github.com/boostorg/math/pull/1167  

Adds GPU support to: sqrt1pm1, erf, erfc, erf_inv, erfc_inv, powm1, lgamma, tgamma, and associated gamma functions.  
  
Removes recursion from lgamma and tgamma by adding dispatch functions to handle the reflection case.

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2024-07-31 19:36:33 UTC  
> Updated_at: 2024-08-06 02:11:01 UTC  
> Url: https://github.com/boostorg/math/pull/1167#issuecomment-2261289424  

## [Codecov](https://app.codecov.io/gh/boostorg/math/pull/1167?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
Attention: Patch coverage is `93.67089%` with `10 lines` in your changes missing coverage. Please review.  
> Project coverage is 94.08%. Comparing base [(`ef3892c`)](https://app.codecov.io/gh/boostorg/math/commit/ef3892c5fb29a667e5cd64a9f94a6a9e1ce39898?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) to head [(`445e36a`)](https://app.codecov.io/gh/boostorg/math/commit/445e36adc0de9bea170669c9724d9b0ac9e3c5c3?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
| [Files](https://app.codecov.io/gh/boostorg/math/pull/1167?dropdown=coverage&src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Patch % | Lines |  
|---|---|---|  
| [include/boost/math/special\_functions/powm1.hpp](https://app.codecov.io/gh/boostorg/math/pull/1167?src=pr&el=tree&filepath=include%2Fboost%2Fmath%2Fspecial_functions%2Fpowm1.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9tYXRoL3NwZWNpYWxfZnVuY3Rpb25zL3Bvd20xLmhwcA==) | 72.22% | [5 Missing :warning: ](https://app.codecov.io/gh/boostorg/math/pull/1167?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) |  
| [include/boost/math/special\_functions/gamma.hpp](https://app.codecov.io/gh/boostorg/math/pull/1167?src=pr&el=tree&filepath=include%2Fboost%2Fmath%2Fspecial_functions%2Fgamma.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9tYXRoL3NwZWNpYWxfZnVuY3Rpb25zL2dhbW1hLmhwcA==) | 94.59% | [4 Missing :warning: ](https://app.codecov.io/gh/boostorg/math/pull/1167?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) |  
| [include/boost/math/special\_functions/erf.hpp](https://app.codecov.io/gh/boostorg/math/pull/1167?src=pr&el=tree&filepath=include%2Fboost%2Fmath%2Fspecial_functions%2Ferf.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9tYXRoL3NwZWNpYWxfZnVuY3Rpb25zL2VyZi5ocHA=) | 94.11% | [1 Missing :warning: ](https://app.codecov.io/gh/boostorg/math/pull/1167?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) |  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/math/pull/1167/graphs/tree.svg?width=650&height=150&src=pr&token=26IDb1OAa7&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/math/pull/1167?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@           Coverage Diff            @@  
##           develop    #1167   +/-   ##  
========================================  
  Coverage    94.07%   94.08%             
========================================  
  Files          780      780             
  Lines        65764    65797   +33       
========================================  
+ Hits         61867    61904   +37       
+ Misses        3897     3893    -4       
```  
  
| [Files](https://app.codecov.io/gh/boostorg/math/pull/1167?dropdown=coverage&src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/math/special\_functions/beta.hpp](https://app.codecov.io/gh/boostorg/math/pull/1167?src=pr&el=tree&filepath=include%2Fboost%2Fmath%2Fspecial_functions%2Fbeta.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9tYXRoL3NwZWNpYWxfZnVuY3Rpb25zL2JldGEuaHBw) | `100.00% <ø> (ø)` | |  
| [...de/boost/math/special\_functions/detail/erf\_inv.hpp](https://app.codecov.io/gh/boostorg/math/pull/1167?src=pr&el=tree&filepath=include%2Fboost%2Fmath%2Fspecial_functions%2Fdetail%2Ferf_inv.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9tYXRoL3NwZWNpYWxfZnVuY3Rpb25zL2RldGFpbC9lcmZfaW52LmhwcA==) | `100.00% <100.00%> (ø)` | |  
| [...ost/math/special\_functions/detail/igamma\_large.hpp](https://app.codecov.io/gh/boostorg/math/pull/1167?src=pr&el=tree&filepath=include%2Fboost%2Fmath%2Fspecial_functions%2Fdetail%2Figamma_large.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9tYXRoL3NwZWNpYWxfZnVuY3Rpb25zL2RldGFpbC9pZ2FtbWFfbGFyZ2UuaHBw) | `33.11% <100.00%> (ø)` | |  
| [...ost/math/special\_functions/detail/lgamma\_small.hpp](https://app.codecov.io/gh/boostorg/math/pull/1167?src=pr&el=tree&filepath=include%2Fboost%2Fmath%2Fspecial_functions%2Fdetail%2Flgamma_small.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9tYXRoL3NwZWNpYWxfZnVuY3Rpb25zL2RldGFpbC9sZ2FtbWFfc21hbGwuaHBw) | `59.90% <100.00%> (+0.54%)` | :arrow_up: |  
| [...h/special\_functions/detail/unchecked\_factorial.hpp](https://app.codecov.io/gh/boostorg/math/pull/1167?src=pr&el=tree&filepath=include%2Fboost%2Fmath%2Fspecial_functions%2Fdetail%2Funchecked_factorial.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9tYXRoL3NwZWNpYWxfZnVuY3Rpb25zL2RldGFpbC91bmNoZWNrZWRfZmFjdG9yaWFsLmhwcA==) | `100.00% <100.00%> (ø)` | |  
| [include/boost/math/special\_functions/sign.hpp](https://app.codecov.io/gh/boostorg/math/pull/1167?src=pr&el=tree&filepath=include%2Fboost%2Fmath%2Fspecial_functions%2Fsign.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9tYXRoL3NwZWNpYWxfZnVuY3Rpb25zL3NpZ24uaHBw) | `100.00% <100.00%> (ø)` | |  
| [include/boost/math/special\_functions/sqrt1pm1.hpp](https://app.codecov.io/gh/boostorg/math/pull/1167?src=pr&el=tree&filepath=include%2Fboost%2Fmath%2Fspecial_functions%2Fsqrt1pm1.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9tYXRoL3NwZWNpYWxfZnVuY3Rpb25zL3NxcnQxcG0xLmhwcA==) | `100.00% <100.00%> (ø)` | |  
| [include/boost/math/tools/fraction.hpp](https://app.codecov.io/gh/boostorg/math/pull/1167?src=pr&el=tree&filepath=include%2Fboost%2Fmath%2Ftools%2Ffraction.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9tYXRoL3Rvb2xzL2ZyYWN0aW9uLmhwcA==) | `91.04% <100.00%> (+0.56%)` | :arrow_up: |  
| [include/boost/math/tools/precision.hpp](https://app.codecov.io/gh/boostorg/math/pull/1167?src=pr&el=tree&filepath=include%2Fboost%2Fmath%2Ftools%2Fprecision.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9tYXRoL3Rvb2xzL3ByZWNpc2lvbi5ocHA=) | `100.00% <100.00%> (ø)` | |  
| [test/test\_erf.cpp](https://app.codecov.io/gh/boostorg/math/pull/1167?src=pr&el=tree&filepath=test%2Ftest_erf.cpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC90ZXN0X2VyZi5jcHA=) | `95.00% <ø> (ø)` | |  
| ... and [7 more](https://app.codecov.io/gh/boostorg/math/pull/1167?src=pr&el=tree-more&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | |  
  
... and [3 files with indirect coverage changes](https://app.codecov.io/gh/boostorg/math/pull/1167/indirect-changes?src=pr&el=tree-more&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/math/pull/1167?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/math/pull/1167?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [ef3892c...445e36a](https://app.codecov.io/gh/boostorg/math/pull/1167?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---

## Comment 2

> Username: mborland  
> Created_at: 2024-07-31 19:48:58 UTC  
> Url: https://github.com/boostorg/math/pull/1167#issuecomment-2261326458  

@jzmaddock Can you see any reason why Windows platforms would not like the diffs for `erfc_inv` and `tgamma`? The former just changes the dispatching method from pointers to `std::integral_constant` to references and the latter adds a dispatching function. I would assume if there was something fundamentally wrong with the way dispatching is occuring it would hit other platforms. Both are throwing on numeric overflow

---

## Comment 3

> Username: dschmitz89  
> Created_at: 2024-08-05 07:18:52 UTC  
> Url: https://github.com/boostorg/math/pull/1167#issuecomment-2268349311  

@mborland : this looks awesome. Could you give a little high level overview of what the goals of these GPU efforts in Boost.Math are? SciPy is doing something similar at the moment and we are also using some special functions from you guys. CC @steppi in case he did not see these recent GPU PRs

---

## Comment 4

> Username: steppi  
> Created_at: 2024-08-05 12:35:29 UTC  
> Updated_at: 2024-08-05 12:35:41 UTC  
> Url: https://github.com/boostorg/math/pull/1167#issuecomment-2268969997  

This is really awesome to see! Thanks for the ping @dschmitz89.  In https://github.com/scipy/scipy/pull/20785#issuecomment-2153442356, you had said  
  
*From a stats maintenance perspective, I would be sad to see scipy dropping boost due to array API concerns. Their implementations of special functions for statistical purposes are a great resource for us, not mentioning their responsiveness to our issues.*  
  
and I'd replied something to the effect that if Boost also supported GPU execution for special functions, then we could keep using Boost's special functions in SciPy without having to maintain separate implementations for other array libraries. @jzmaddock thought that was reasonable and now here we are. I'll play around with getting Boost special functions working in CuPy, and it looks like we'll be able to use them directly there, and aso use them internally in the `xsf` special function library @izaid and I are working on.

---

## Comment 5

> Username: mborland  
> Created_at: 2024-08-05 12:51:59 UTC  
> Url: https://github.com/boostorg/math/pull/1167#issuecomment-2269003520  

> @mborland : this looks awesome. Could you give a little high level overview of what the goals of these GPU efforts in Boost.Math are? SciPy is doing something similar at the moment and we are also using some special functions from you guys.   
  
I think Albert's summary is pretty good. @jzmaddock has a quite old PR adding CUDA support here: https://github.com/boostorg/math/pull/127 so I have been extracting from and expanding on that. The development on this repo: https://github.com/cppalliance/cuda-math since my employer can only directly add GPU enabled CI there, and then cherry picking onto boost proper. Two questions for you two @dschmitz89 and @steppi   
  
1) Is there a specific prioritization of functions or distributions that would benefit you?   
  
2) Are you looking for CUDA only or are there other platforms that you need/want covered? We have been inserting both CUDA and SYCL support so far. SYCL has some more significant limitations like no recursion. So far it's been relatively easy to work around by adding dispatching functions but there will be functions that need to be completely re-written or will remain unsupported such as anything involving root finding so I'd rather punt rewrites until there is real demand.

---

## Comment 6

> Username: izaid  
> Created_at: 2024-08-05 13:09:32 UTC  
> Url: https://github.com/boostorg/math/pull/1167#issuecomment-2269041101  

I think this is great too! I'll let @steppi answer any specific prioritization of functions, but the ones you've done so far look good to me. I think we really only care about CUDA for now, but others may think differently.  
  
@mborland I wanted to throw in one point though. Are you testing these using just nvcc, or nvrtc as well? nvrtc is CUDA's runtime (JIT) compiler, and is what CuPy uses for instance. It has more stringent requirements...

---

## Comment 7

> Username: steppi  
> Created_at: 2024-08-05 13:10:06 UTC  
> Updated_at: 2024-08-05 13:19:50 UTC  
> Url: https://github.com/boostorg/math/pull/1167#issuecomment-2269042223  

> 2\. anything involving root finding  
  
I’m trying to figure out root finding that will work well on GPU now. I’ll keep you posted on my progress.

---

## Comment 8

> Username: mborland  
> Created_at: 2024-08-05 13:29:58 UTC  
> Url: https://github.com/boostorg/math/pull/1167#issuecomment-2269084546  

> @mborland I wanted to throw in one point though. Are you testing these using just nvcc, or nvrtc as well? nvrtc is CUDA's runtime (JIT) compiler, and is what CuPy uses for instance. It has more stringent requirements...  
  
Right now it's just NVCC but we can look into adding NVRTC as well. Tracked here: https://github.com/cppalliance/cuda-math/issues/7.  
  
> I’m trying to figure out root finding that will work well on GPU now. I’ll keep you posted on my progress.  
  
Cool. Thank you

---

## Comment 9

> Username: dschmitz89  
> Created_at: 2024-08-05 14:51:20 UTC  
> Url: https://github.com/boostorg/math/pull/1167#issuecomment-2269267314  

Nice that the conversation is starting. :) On the SciPy side I would defer to @steppi for all technical details as I don't have expertise in CUDA or special function computations.

---

## Comment 10

> Username: mborland  
> Created_at: 2024-08-06 13:01:00 UTC  
> Url: https://github.com/boostorg/math/pull/1167#issuecomment-2271237240  

> Nice that the conversation is starting. :) On the SciPy side I would defer to @steppi for all technical details as I don't have expertise in CUDA or special function computations.  
  
Feel free to open further issues/discussion here or downstream at: https://github.com/cppalliance/cuda-math. Either way it will get seen.   
  
Only CI failure is from codecov so I am going to merge this one in.

---
