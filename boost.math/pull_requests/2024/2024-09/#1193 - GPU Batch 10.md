# #1193 GPU Batch 10 [Merged]

> Username: mborland  
> Created at: 2024-09-06 16:26:00 UTC  
> Updated at: 2024-09-09 16:58:11 UTC  
> Merged at: 2024-09-09 16:55:41 UTC  
> Closed at: 2024-09-09 16:55:41 UTC  
> Url: https://github.com/boostorg/math/pull/1193  

The focus of this PR was to round out support for the distribution now that many of the fundamental special functions have GPU support. Adds support to the following distributions: inverse gaussian, lognormal, non central beta, non central f, non central chi squared, negative binomial, normal, pareto, poisson, rayleigh, students t, triangular and uniform.  
  
The three remaining unsupported distributions are the hyperexponential, hypergeometric, and non-central t. The first two need  related special functions support. Non-central T needs GPU support for quadrature built out, specifically exp-sinh. My initial thoughts are restricting quadrature to NVCC and NVRTC based on available APIs for memory allocations, but we will see.  
  
On device CI runs can be found: https://github.com/cppalliance/cuda-math/pull/21.  
  
CC: @dschmitz89, @izaid, @steppi.

---

## Comment 1

> Username: jzmaddock  
> Created_at: 2024-09-06 17:02:34 UTC  
> Url: https://github.com/boostorg/math/pull/1193#issuecomment-2334473688  

The use of quadrature in the non central T is a new addition to improve accuracy in the tails: IMO it would not be unreasonable to disable this on the GPU.  Likewise there may be other functions which need slimming down for GPU usage, even though that may mean a lower quality of implementation.  The trick is deciding where to draw the line ;)

---

## Comment 2

> Username: mborland  
> Created_at: 2024-09-06 17:23:06 UTC  
> Url: https://github.com/boostorg/math/pull/1193#issuecomment-2334506832  

> The use of quadrature in the non central T is a new addition to improve accuracy in the tails: IMO it would not be unreasonable to disable this on the GPU. Likewise there may be other functions which need slimming down for GPU usage, even though that may mean a lower quality of implementation. The trick is deciding where to draw the line ;)  
  
That makes sense. Now that we have more substantial testing there are some clear outliers appearing in testing run time: https://github.com/cppalliance/cuda-math/actions/runs/10741618717/job/29792448169?pr=21#step:9:262. Once things are correct we can take optimization steps. @NAThompson is also interested in the prospect of the quadrature algorithms being used by others on device.

---

## Comment 3

> Username: codecov[bot]  
> Created_at: 2024-09-06 17:24:11 UTC  
> Updated_at: 2024-09-09 16:58:11 UTC  
> Url: https://github.com/boostorg/math/pull/1193#issuecomment-2334508311  

## [Codecov](https://app.codecov.io/gh/boostorg/math/pull/1193?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
Attention: Patch coverage is `97.26316%` with `13 lines` in your changes missing coverage. Please review.  
> Project coverage is 93.79%. Comparing base [(`1e9b2cc`)](https://app.codecov.io/gh/boostorg/math/commit/1e9b2ccbf99f01bdceb02c1e1bbd270578d1d469?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) to head [(`e9cd6c9`)](https://app.codecov.io/gh/boostorg/math/commit/e9cd6c96fd5c9606e3238b3a4ed3bbce1f945c7f?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> Report is 6 commits behind head on develop.  
  
| [Files with missing lines](https://app.codecov.io/gh/boostorg/math/pull/1193?dropdown=coverage&src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Patch % | Lines |  
|---|---|---|  
| [include/boost/math/distributions/rayleigh.hpp](https://app.codecov.io/gh/boostorg/math/pull/1193?src=pr&el=tree&filepath=include%2Fboost%2Fmath%2Fdistributions%2Frayleigh.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9tYXRoL2Rpc3RyaWJ1dGlvbnMvcmF5bGVpZ2guaHBw) | 89.18% | [4 Missing :warning: ](https://app.codecov.io/gh/boostorg/math/pull/1193?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) |  
| [...lude/boost/math/distributions/non\_central\_beta.hpp](https://app.codecov.io/gh/boostorg/math/pull/1193?src=pr&el=tree&filepath=include%2Fboost%2Fmath%2Fdistributions%2Fnon_central_beta.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9tYXRoL2Rpc3RyaWJ1dGlvbnMvbm9uX2NlbnRyYWxfYmV0YS5ocHA=) | 93.75% | [3 Missing :warning: ](https://app.codecov.io/gh/boostorg/math/pull/1193?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) |  
| [include/boost/math/distributions/students\_t.hpp](https://app.codecov.io/gh/boostorg/math/pull/1193?src=pr&el=tree&filepath=include%2Fboost%2Fmath%2Fdistributions%2Fstudents_t.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9tYXRoL2Rpc3RyaWJ1dGlvbnMvc3R1ZGVudHNfdC5ocHA=) | 91.30% | [2 Missing :warning: ](https://app.codecov.io/gh/boostorg/math/pull/1193?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) |  
| [...e/boost/math/distributions/detail/generic\_mode.hpp](https://app.codecov.io/gh/boostorg/math/pull/1193?src=pr&el=tree&filepath=include%2Fboost%2Fmath%2Fdistributions%2Fdetail%2Fgeneric_mode.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9tYXRoL2Rpc3RyaWJ1dGlvbnMvZGV0YWlsL2dlbmVyaWNfbW9kZS5ocHA=) | 83.33% | [1 Missing :warning: ](https://app.codecov.io/gh/boostorg/math/pull/1193?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) |  
| [...lude/boost/math/distributions/inverse\_gaussian.hpp](https://app.codecov.io/gh/boostorg/math/pull/1193?src=pr&el=tree&filepath=include%2Fboost%2Fmath%2Fdistributions%2Finverse_gaussian.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9tYXRoL2Rpc3RyaWJ1dGlvbnMvaW52ZXJzZV9nYXVzc2lhbi5ocHA=) | 97.22% | [1 Missing :warning: ](https://app.codecov.io/gh/boostorg/math/pull/1193?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) |  
| [include/boost/math/distributions/pareto.hpp](https://app.codecov.io/gh/boostorg/math/pull/1193?src=pr&el=tree&filepath=include%2Fboost%2Fmath%2Fdistributions%2Fpareto.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9tYXRoL2Rpc3RyaWJ1dGlvbnMvcGFyZXRvLmhwcA==) | 97.50% | [1 Missing :warning: ](https://app.codecov.io/gh/boostorg/math/pull/1193?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) |  
| [include/boost/math/distributions/poisson.hpp](https://app.codecov.io/gh/boostorg/math/pull/1193?src=pr&el=tree&filepath=include%2Fboost%2Fmath%2Fdistributions%2Fpoisson.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9tYXRoL2Rpc3RyaWJ1dGlvbnMvcG9pc3Nvbi5ocHA=) | 96.00% | [1 Missing :warning: ](https://app.codecov.io/gh/boostorg/math/pull/1193?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) |  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/math/pull/1193/graphs/tree.svg?width=650&height=150&src=pr&token=26IDb1OAa7&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/math/pull/1193?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@           Coverage Diff            @@  
##           develop    #1193   +/-   ##  
========================================  
  Coverage    93.78%   93.79%             
========================================  
  Files          654      655    +1       
  Lines        53774    53851   +77       
========================================  
+ Hits         50431    50508   +77       
  Misses        3343     3343             
```  
  
| [Files with missing lines](https://app.codecov.io/gh/boostorg/math/pull/1193?dropdown=coverage&src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [...ath/distributions/detail/common\_error\_handling.hpp](https://app.codecov.io/gh/boostorg/math/pull/1193?src=pr&el=tree&filepath=include%2Fboost%2Fmath%2Fdistributions%2Fdetail%2Fcommon_error_handling.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9tYXRoL2Rpc3RyaWJ1dGlvbnMvZGV0YWlsL2NvbW1vbl9lcnJvcl9oYW5kbGluZy5ocHA=) | `82.14% <100.00%> (ø)` | |  
| [...ost/math/distributions/detail/generic\_quantile.hpp](https://app.codecov.io/gh/boostorg/math/pull/1193?src=pr&el=tree&filepath=include%2Fboost%2Fmath%2Fdistributions%2Fdetail%2Fgeneric_quantile.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9tYXRoL2Rpc3RyaWJ1dGlvbnMvZGV0YWlsL2dlbmVyaWNfcXVhbnRpbGUuaHBw) | `83.87% <100.00%> (ø)` | |  
| [...ath/distributions/detail/inv\_discrete\_quantile.hpp](https://app.codecov.io/gh/boostorg/math/pull/1193?src=pr&el=tree&filepath=include%2Fboost%2Fmath%2Fdistributions%2Fdetail%2Finv_discrete_quantile.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9tYXRoL2Rpc3RyaWJ1dGlvbnMvZGV0YWlsL2ludl9kaXNjcmV0ZV9xdWFudGlsZS5ocHA=) | `88.55% <100.00%> (+0.05%)` | :arrow_up: |  
| [include/boost/math/distributions/lognormal.hpp](https://app.codecov.io/gh/boostorg/math/pull/1193?src=pr&el=tree&filepath=include%2Fboost%2Fmath%2Fdistributions%2Flognormal.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9tYXRoL2Rpc3RyaWJ1dGlvbnMvbG9nbm9ybWFsLmhwcA==) | `81.81% <100.00%> (+0.61%)` | :arrow_up: |  
| [...ude/boost/math/distributions/negative\_binomial.hpp](https://app.codecov.io/gh/boostorg/math/pull/1193?src=pr&el=tree&filepath=include%2Fboost%2Fmath%2Fdistributions%2Fnegative_binomial.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9tYXRoL2Rpc3RyaWJ1dGlvbnMvbmVnYXRpdmVfYmlub21pYWwuaHBw) | `89.74% <100.00%> (+0.49%)` | :arrow_up: |  
| [...ost/math/distributions/non\_central\_chi\_squared.hpp](https://app.codecov.io/gh/boostorg/math/pull/1193?src=pr&el=tree&filepath=include%2Fboost%2Fmath%2Fdistributions%2Fnon_central_chi_squared.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9tYXRoL2Rpc3RyaWJ1dGlvbnMvbm9uX2NlbnRyYWxfY2hpX3NxdWFyZWQuaHBw) | `95.53% <100.00%> (+0.03%)` | :arrow_up: |  
| [include/boost/math/distributions/non\_central\_f.hpp](https://app.codecov.io/gh/boostorg/math/pull/1193?src=pr&el=tree&filepath=include%2Fboost%2Fmath%2Fdistributions%2Fnon_central_f.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9tYXRoL2Rpc3RyaWJ1dGlvbnMvbm9uX2NlbnRyYWxfZi5ocHA=) | `85.50% <100.00%> (+0.07%)` | :arrow_up: |  
| [include/boost/math/distributions/normal.hpp](https://app.codecov.io/gh/boostorg/math/pull/1193?src=pr&el=tree&filepath=include%2Fboost%2Fmath%2Fdistributions%2Fnormal.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9tYXRoL2Rpc3RyaWJ1dGlvbnMvbm9ybWFsLmhwcA==) | `85.92% <100.00%> (+0.76%)` | :arrow_up: |  
| [include/boost/math/distributions/triangular.hpp](https://app.codecov.io/gh/boostorg/math/pull/1193?src=pr&el=tree&filepath=include%2Fboost%2Fmath%2Fdistributions%2Ftriangular.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9tYXRoL2Rpc3RyaWJ1dGlvbnMvdHJpYW5ndWxhci5ocHA=) | `89.04% <100.00%> (+0.63%)` | :arrow_up: |  
| [include/boost/math/distributions/uniform.hpp](https://app.codecov.io/gh/boostorg/math/pull/1193?src=pr&el=tree&filepath=include%2Fboost%2Fmath%2Fdistributions%2Funiform.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9tYXRoL2Rpc3RyaWJ1dGlvbnMvdW5pZm9ybS5ocHA=) | `84.82% <100.00%> (ø)` | |  
| ... and [27 more](https://app.codecov.io/gh/boostorg/math/pull/1193?src=pr&el=tree-more&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | |  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/math/pull/1193?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/math/pull/1193?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [1e9b2cc...e9cd6c9](https://app.codecov.io/gh/boostorg/math/pull/1193?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---

## Comment 4

> Username: jzmaddock  
> Created_at: 2024-09-06 17:59:10 UTC  
> Url: https://github.com/boostorg/math/pull/1193#issuecomment-2334563478  

>  @NAThompson is also interested in the prospect of the quadrature algorithms being used by others on device.  
  
Nod, that's actually a good use case given that the algorithms are in principle massively parallel.  In that case it probably makes more sense for the Integrand to be on the device, and the quadrature routine the glue that pulls the results together?

---

## Comment 5

> Username: mborland  
> Created_at: 2024-09-06 18:19:15 UTC  
> Url: https://github.com/boostorg/math/pull/1193#issuecomment-2334592541  

> > @NAThompson is also interested in the prospect of the quadrature algorithms being used by others on device.  
>   
> Nod, that's actually a good use case given that the algorithms are in principle massively parallel. In that case it probably makes more sense for the Integrand to be on the device, and the quadrature routine the glue that pulls the results together?  
  
I think so. I have to work through how to make a shared memory region on the device that won't be leaked. Right now nick uses a shared_ptr to vectors with all the data. Neither of those facilities are available on device. CUDA has a version if malloc and free which make it easy enough to implement vector, but a shared pointer is more involved.

---
