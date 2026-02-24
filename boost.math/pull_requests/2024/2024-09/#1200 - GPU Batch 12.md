# #1200 GPU Batch 12 [Merged]

> Username: mborland  
> Created at: 2024-09-16 18:14:07 UTC  
> Updated at: 2024-09-17 19:23:17 UTC  
> Merged at: 2024-09-17 19:23:07 UTC  
> Closed at: 2024-09-17 19:23:07 UTC  
> Url: https://github.com/boostorg/math/pull/1200  

This PR adds support to the airy functions: `airy_ai`, `airy_ai_prime`, `airy_ai_zero`, `airy_bi`, `airy_bi_prime`, `airy_bi_zero`, and the entire ellint family: `ellint_1`, `ellint_2`, `ellint_3`, `ellint_d`, `heuman_lambda`, `jacobi_zeta`, `ellint_rf`, `ellint_rd`, `ellint_rj`, `ellint_rc`, and `ellint_rg`. The only outlier in all of this is `ellint_3` only supports CUDA (NVCC and NVRTC) and is annotated as such, I couldn't quite figure out how to remove all of the recursion to get SYCL running since there are a large number of edge cases handled in the implementation.  
  
On device testing can be found here: https://github.com/cppalliance/cuda-math/pull/26  
  
CC: @dschmitz89 , @izaid, @steppi

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2024-09-16 20:13:01 UTC  
> Updated_at: 2024-09-17 19:23:17 UTC  
> Url: https://github.com/boostorg/math/pull/1200#issuecomment-2353847836  

## [Codecov](https://app.codecov.io/gh/boostorg/math/pull/1200?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
Attention: Patch coverage is `99.25373%` with `1 line` in your changes missing coverage. Please review.  
> Project coverage is 93.78%. Comparing base [(`ff72eaa`)](https://app.codecov.io/gh/boostorg/math/commit/ff72eaad44ffddfa25b01815a7bb1730b3cb81e7?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) to head [(`dbb547b`)](https://app.codecov.io/gh/boostorg/math/commit/dbb547b5ac051b698331af342511a005e9b9505c?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> Report is 4 commits behind head on develop.  
  
| [Files with missing lines](https://app.codecov.io/gh/boostorg/math/pull/1200?dropdown=coverage&src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Patch % | Lines |  
|---|---|---|  
| [include/boost/math/special\_functions/ellint\_rj.hpp](https://app.codecov.io/gh/boostorg/math/pull/1200?src=pr&el=tree&filepath=include%2Fboost%2Fmath%2Fspecial_functions%2Fellint_rj.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9tYXRoL3NwZWNpYWxfZnVuY3Rpb25zL2VsbGludF9yai5ocHA=) | 97.36% | [1 Missing :warning: ](https://app.codecov.io/gh/boostorg/math/pull/1200?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) |  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/math/pull/1200/graphs/tree.svg?width=650&height=150&src=pr&token=26IDb1OAa7&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/math/pull/1200?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@             Coverage Diff             @@  
##           develop    #1200      +/-   ##  
===========================================  
- Coverage    93.80%   93.78%   -0.02%       
===========================================  
  Files          657      657                
  Lines        53881    53899      +18       
===========================================  
+ Hits         50541    50551      +10       
- Misses        3340     3348       +8       
```  
  
| [Files with missing lines](https://app.codecov.io/gh/boostorg/math/pull/1200?dropdown=coverage&src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/math/special\_functions/airy.hpp](https://app.codecov.io/gh/boostorg/math/pull/1200?src=pr&el=tree&filepath=include%2Fboost%2Fmath%2Fspecial_functions%2Fairy.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9tYXRoL3NwZWNpYWxfZnVuY3Rpb25zL2FpcnkuaHBw) | `100.00% <100.00%> (ø)` | |  
| [include/boost/math/special\_functions/atanh.hpp](https://app.codecov.io/gh/boostorg/math/pull/1200?src=pr&el=tree&filepath=include%2Fboost%2Fmath%2Fspecial_functions%2Fatanh.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9tYXRoL3NwZWNpYWxfZnVuY3Rpb25zL2F0YW5oLmhwcA==) | `100.00% <100.00%> (ø)` | |  
| [include/boost/math/special\_functions/ellint\_1.hpp](https://app.codecov.io/gh/boostorg/math/pull/1200?src=pr&el=tree&filepath=include%2Fboost%2Fmath%2Fspecial_functions%2Fellint_1.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9tYXRoL3NwZWNpYWxfZnVuY3Rpb25zL2VsbGludF8xLmhwcA==) | `100.00% <100.00%> (ø)` | |  
| [include/boost/math/special\_functions/ellint\_2.hpp](https://app.codecov.io/gh/boostorg/math/pull/1200?src=pr&el=tree&filepath=include%2Fboost%2Fmath%2Fspecial_functions%2Fellint_2.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9tYXRoL3NwZWNpYWxfZnVuY3Rpb25zL2VsbGludF8yLmhwcA==) | `100.00% <100.00%> (ø)` | |  
| [include/boost/math/special\_functions/ellint\_3.hpp](https://app.codecov.io/gh/boostorg/math/pull/1200?src=pr&el=tree&filepath=include%2Fboost%2Fmath%2Fspecial_functions%2Fellint_3.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9tYXRoL3NwZWNpYWxfZnVuY3Rpb25zL2VsbGludF8zLmhwcA==) | `100.00% <100.00%> (ø)` | |  
| [include/boost/math/special\_functions/ellint\_d.hpp](https://app.codecov.io/gh/boostorg/math/pull/1200?src=pr&el=tree&filepath=include%2Fboost%2Fmath%2Fspecial_functions%2Fellint_d.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9tYXRoL3NwZWNpYWxfZnVuY3Rpb25zL2VsbGludF9kLmhwcA==) | `94.00% <100.00%> (-6.00%)` | :arrow_down: |  
| [include/boost/math/special\_functions/ellint\_rc.hpp](https://app.codecov.io/gh/boostorg/math/pull/1200?src=pr&el=tree&filepath=include%2Fboost%2Fmath%2Fspecial_functions%2Fellint_rc.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9tYXRoL3NwZWNpYWxfZnVuY3Rpb25zL2VsbGludF9yYy5ocHA=) | `100.00% <100.00%> (ø)` | |  
| [include/boost/math/special\_functions/ellint\_rd.hpp](https://app.codecov.io/gh/boostorg/math/pull/1200?src=pr&el=tree&filepath=include%2Fboost%2Fmath%2Fspecial_functions%2Fellint_rd.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9tYXRoL3NwZWNpYWxfZnVuY3Rpb25zL2VsbGludF9yZC5ocHA=) | `100.00% <100.00%> (ø)` | |  
| [include/boost/math/special\_functions/ellint\_rf.hpp](https://app.codecov.io/gh/boostorg/math/pull/1200?src=pr&el=tree&filepath=include%2Fboost%2Fmath%2Fspecial_functions%2Fellint_rf.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9tYXRoL3NwZWNpYWxfZnVuY3Rpb25zL2VsbGludF9yZi5ocHA=) | `100.00% <100.00%> (ø)` | |  
| [include/boost/math/special\_functions/ellint\_rg.hpp](https://app.codecov.io/gh/boostorg/math/pull/1200?src=pr&el=tree&filepath=include%2Fboost%2Fmath%2Fspecial_functions%2Fellint_rg.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9tYXRoL3NwZWNpYWxfZnVuY3Rpb25zL2VsbGludF9yZy5ocHA=) | `100.00% <100.00%> (ø)` | |  
| ... and [15 more](https://app.codecov.io/gh/boostorg/math/pull/1200?src=pr&el=tree-more&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | |  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/math/pull/1200?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/math/pull/1200?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [ff72eaa...dbb547b](https://app.codecov.io/gh/boostorg/math/pull/1200?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---
