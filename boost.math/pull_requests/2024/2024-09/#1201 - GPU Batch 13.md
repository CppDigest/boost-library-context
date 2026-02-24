# #1201 GPU Batch 13 [Merged]

> Username: mborland  
> Created at: 2024-09-17 19:29:42 UTC  
> Updated at: 2024-09-19 13:57:43 UTC  
> Merged at: 2024-09-19 13:56:43 UTC  
> Closed at: 2024-09-19 13:56:43 UTC  
> Url: https://github.com/boostorg/math/pull/1201  

This PR resumes work on special functions and adds support for: `expint`, `gegenbauer`, `gegenbauer_prime`, `gegenbauer_derivative`, `cyl_hankel_1`, `cyl_hankel_2`, `sph_hankel_1`, `sph_hankel_2`, and `hermite`.  
  
In the docs I added a note that states in the NVCC or NVRTC case the return type of cyl and sph hankels is in namespace `cuda::std::` instead of `std::` so you get a `cuda::std::complex<T>` as the result. Much like other utilities developed so far `boost::math::complex<T>` is just an alias to the proper type depending on context.  
  
On device CI runs can be found: https://github.com/cppalliance/cuda-math/pull/27  
  
CC: @dschmitz89, @izaid, @steppi

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2024-09-18 15:18:12 UTC  
> Updated_at: 2024-09-19 13:57:43 UTC  
> Url: https://github.com/boostorg/math/pull/1201#issuecomment-2358762117  

## [Codecov](https://app.codecov.io/gh/boostorg/math/pull/1201?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
Attention: Patch coverage is `96.55172%` with `2 lines` in your changes missing coverage. Please review.  
> Project coverage is 93.78%. Comparing base [(`c3afa49`)](https://app.codecov.io/gh/boostorg/math/commit/c3afa49c9a66523bd9be47888088337d0229a7cb?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) to head [(`9f21419`)](https://app.codecov.io/gh/boostorg/math/commit/9f21419b2dcd7be3387423c015c76b5ccd451c32?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> Report is 4 commits behind head on develop.  
  
| [Files with missing lines](https://app.codecov.io/gh/boostorg/math/pull/1201?dropdown=coverage&src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Patch % | Lines |  
|---|---|---|  
| [include/boost/math/special\_functions/hankel.hpp](https://app.codecov.io/gh/boostorg/math/pull/1201?src=pr&el=tree&filepath=include%2Fboost%2Fmath%2Fspecial_functions%2Fhankel.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9tYXRoL3NwZWNpYWxfZnVuY3Rpb25zL2hhbmtlbC5ocHA=) | 92.59% | [2 Missing :warning: ](https://app.codecov.io/gh/boostorg/math/pull/1201?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) |  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/math/pull/1201/graphs/tree.svg?width=650&height=150&src=pr&token=26IDb1OAa7&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/math/pull/1201?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@           Coverage Diff            @@  
##           develop    #1201   +/-   ##  
========================================  
  Coverage    93.78%   93.78%             
========================================  
  Files          657      657             
  Lines        53899    53903    +4       
========================================  
+ Hits         50551    50555    +4       
  Misses        3348     3348             
```  
  
| [Files with missing lines](https://app.codecov.io/gh/boostorg/math/pull/1201?dropdown=coverage&src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/math/special\_functions/bessel.hpp](https://app.codecov.io/gh/boostorg/math/pull/1201?src=pr&el=tree&filepath=include%2Fboost%2Fmath%2Fspecial_functions%2Fbessel.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9tYXRoL3NwZWNpYWxfZnVuY3Rpb25zL2Jlc3NlbC5ocHA=) | `100.00% <ø> (ø)` | |  
| [include/boost/math/special\_functions/expint.hpp](https://app.codecov.io/gh/boostorg/math/pull/1201?src=pr&el=tree&filepath=include%2Fboost%2Fmath%2Fspecial_functions%2Fexpint.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9tYXRoL3NwZWNpYWxfZnVuY3Rpb25zL2V4cGludC5ocHA=) | `95.41% <100.00%> (+0.06%)` | :arrow_up: |  
| [include/boost/math/special\_functions/hermite.hpp](https://app.codecov.io/gh/boostorg/math/pull/1201?src=pr&el=tree&filepath=include%2Fboost%2Fmath%2Fspecial_functions%2Fhermite.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9tYXRoL3NwZWNpYWxfZnVuY3Rpb25zL2hlcm1pdGUuaHBw) | `100.00% <100.00%> (ø)` | |  
| [test/test\_expint.cpp](https://app.codecov.io/gh/boostorg/math/pull/1201?src=pr&el=tree&filepath=test%2Ftest_expint.cpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC90ZXN0X2V4cGludC5jcHA=) | `100.00% <ø> (ø)` | |  
| [test/test\_expint.hpp](https://app.codecov.io/gh/boostorg/math/pull/1201?src=pr&el=tree&filepath=test%2Ftest_expint.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC90ZXN0X2V4cGludC5ocHA=) | `100.00% <ø> (ø)` | |  
| [test/test\_hankel.cpp](https://app.codecov.io/gh/boostorg/math/pull/1201?src=pr&el=tree&filepath=test%2Ftest_hankel.cpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC90ZXN0X2hhbmtlbC5jcHA=) | `100.00% <ø> (ø)` | |  
| [test/test\_hermite.cpp](https://app.codecov.io/gh/boostorg/math/pull/1201?src=pr&el=tree&filepath=test%2Ftest_hermite.cpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC90ZXN0X2hlcm1pdGUuY3Bw) | `93.75% <ø> (ø)` | |  
| [test/test\_hermite.hpp](https://app.codecov.io/gh/boostorg/math/pull/1201?src=pr&el=tree&filepath=test%2Ftest_hermite.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC90ZXN0X2hlcm1pdGUuaHBw) | `100.00% <ø> (ø)` | |  
| [include/boost/math/special\_functions/hankel.hpp](https://app.codecov.io/gh/boostorg/math/pull/1201?src=pr&el=tree&filepath=include%2Fboost%2Fmath%2Fspecial_functions%2Fhankel.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9tYXRoL3NwZWNpYWxfZnVuY3Rpb25zL2hhbmtlbC5ocHA=) | `94.11% <92.59%> (+0.11%)` | :arrow_up: |  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/math/pull/1201?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/math/pull/1201?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [c3afa49...9f21419](https://app.codecov.io/gh/boostorg/math/pull/1201?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---
