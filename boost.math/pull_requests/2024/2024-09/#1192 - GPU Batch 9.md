# #1192 GPU Batch 9 [Merged]

> Username: mborland  
> Created at: 2024-09-04 15:11:15 UTC  
> Updated at: 2024-09-06 15:28:57 UTC  
> Merged at: 2024-09-06 15:27:56 UTC  
> Closed at: 2024-09-06 15:27:56 UTC  
> Url: https://github.com/boostorg/math/pull/1192  

In this PR we continue to build out support for the distributions now that we have complete implementations of the beta and gamma function families. Adds support for: fisher f dist, gamma dist, geometric dist, inverse chi squared dist, and inverse gamma dist.  
  
Minor change to the testing is to reduce the number of threads per block from 512 to 256. The CI system started to error on lack of resources.   
  
Completed CI runs can be found here: https://github.com/cppalliance/cuda-math/pull/20

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2024-09-04 16:19:30 UTC  
> Updated_at: 2024-09-06 15:28:57 UTC  
> Url: https://github.com/boostorg/math/pull/1192#issuecomment-2329491232  

## [Codecov](https://app.codecov.io/gh/boostorg/math/pull/1192?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
Attention: Patch coverage is `98.31461%` with `3 lines` in your changes missing coverage. Please review.  
> Project coverage is 94.09%. Comparing base [(`ab57b20`)](https://app.codecov.io/gh/boostorg/math/commit/ab57b2093efe2de49dac9849065f01b290793be4?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) to head [(`d01893d`)](https://app.codecov.io/gh/boostorg/math/commit/d01893d215f9069849f34c667c467b1112882e64?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> Report is 12 commits behind head on develop.  
  
| [Files with missing lines](https://app.codecov.io/gh/boostorg/math/pull/1192?dropdown=coverage&src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Patch % | Lines |  
|---|---|---|  
| [include/boost/math/distributions/geometric.hpp](https://app.codecov.io/gh/boostorg/math/pull/1192?src=pr&el=tree&filepath=include%2Fboost%2Fmath%2Fdistributions%2Fgeometric.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9tYXRoL2Rpc3RyaWJ1dGlvbnMvZ2VvbWV0cmljLmhwcA==) | 94.87% | [2 Missing :warning: ](https://app.codecov.io/gh/boostorg/math/pull/1192?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) |  
| [include/boost/math/distributions/gamma.hpp](https://app.codecov.io/gh/boostorg/math/pull/1192?src=pr&el=tree&filepath=include%2Fboost%2Fmath%2Fdistributions%2Fgamma.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9tYXRoL2Rpc3RyaWJ1dGlvbnMvZ2FtbWEuaHBw) | 97.14% | [1 Missing :warning: ](https://app.codecov.io/gh/boostorg/math/pull/1192?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) |  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/math/pull/1192/graphs/tree.svg?width=650&height=150&src=pr&token=26IDb1OAa7&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/math/pull/1192?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@           Coverage Diff            @@  
##           develop    #1192   +/-   ##  
========================================  
  Coverage    94.09%   94.09%             
========================================  
  Files          780      780             
  Lines        65851    65907   +56       
========================================  
+ Hits         61960    62016   +56       
  Misses        3891     3891             
```  
  
| [Files with missing lines](https://app.codecov.io/gh/boostorg/math/pull/1192?dropdown=coverage&src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [...ath/distributions/detail/inv\_discrete\_quantile.hpp](https://app.codecov.io/gh/boostorg/math/pull/1192?src=pr&el=tree&filepath=include%2Fboost%2Fmath%2Fdistributions%2Fdetail%2Finv_discrete_quantile.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9tYXRoL2Rpc3RyaWJ1dGlvbnMvZGV0YWlsL2ludl9kaXNjcmV0ZV9xdWFudGlsZS5ocHA=) | `88.50% <100.00%> (ø)` | |  
| [include/boost/math/distributions/fisher\_f.hpp](https://app.codecov.io/gh/boostorg/math/pull/1192?src=pr&el=tree&filepath=include%2Fboost%2Fmath%2Fdistributions%2Ffisher_f.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9tYXRoL2Rpc3RyaWJ1dGlvbnMvZmlzaGVyX2YuaHBw) | `84.61% <100.00%> (+0.98%)` | :arrow_up: |  
| [...e/boost/math/distributions/inverse\_chi\_squared.hpp](https://app.codecov.io/gh/boostorg/math/pull/1192?src=pr&el=tree&filepath=include%2Fboost%2Fmath%2Fdistributions%2Finverse_chi_squared.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9tYXRoL2Rpc3RyaWJ1dGlvbnMvaW52ZXJzZV9jaGlfc3F1YXJlZC5ocHA=) | `91.48% <100.00%> (+0.72%)` | :arrow_up: |  
| [include/boost/math/distributions/inverse\_gamma.hpp](https://app.codecov.io/gh/boostorg/math/pull/1192?src=pr&el=tree&filepath=include%2Fboost%2Fmath%2Fdistributions%2Finverse_gamma.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9tYXRoL2Rpc3RyaWJ1dGlvbnMvaW52ZXJzZV9nYW1tYS5ocHA=) | `80.30% <100.00%> (+1.27%)` | :arrow_up: |  
| [test/test\_fisher\_f.cpp](https://app.codecov.io/gh/boostorg/math/pull/1192?src=pr&el=tree&filepath=test%2Ftest_fisher_f.cpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC90ZXN0X2Zpc2hlcl9mLmNwcA==) | `100.00% <ø> (ø)` | |  
| [test/test\_gamma\_dist.cpp](https://app.codecov.io/gh/boostorg/math/pull/1192?src=pr&el=tree&filepath=test%2Ftest_gamma_dist.cpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC90ZXN0X2dhbW1hX2Rpc3QuY3Bw) | `100.00% <ø> (ø)` | |  
| [test/test\_geometric.cpp](https://app.codecov.io/gh/boostorg/math/pull/1192?src=pr&el=tree&filepath=test%2Ftest_geometric.cpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC90ZXN0X2dlb21ldHJpYy5jcHA=) | `100.00% <ø> (ø)` | |  
| [test/test\_inverse\_chi\_squared\_distribution.cpp](https://app.codecov.io/gh/boostorg/math/pull/1192?src=pr&el=tree&filepath=test%2Ftest_inverse_chi_squared_distribution.cpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC90ZXN0X2ludmVyc2VfY2hpX3NxdWFyZWRfZGlzdHJpYnV0aW9uLmNwcA==) | `100.00% <ø> (ø)` | |  
| [test/test\_inverse\_gamma\_distribution.cpp](https://app.codecov.io/gh/boostorg/math/pull/1192?src=pr&el=tree&filepath=test%2Ftest_inverse_gamma_distribution.cpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC90ZXN0X2ludmVyc2VfZ2FtbWFfZGlzdHJpYnV0aW9uLmNwcA==) | `100.00% <ø> (ø)` | |  
| [include/boost/math/distributions/gamma.hpp](https://app.codecov.io/gh/boostorg/math/pull/1192?src=pr&el=tree&filepath=include%2Fboost%2Fmath%2Fdistributions%2Fgamma.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9tYXRoL2Rpc3RyaWJ1dGlvbnMvZ2FtbWEuaHBw) | `83.33% <97.14%> (+1.31%)` | :arrow_up: |  
| ... and [1 more](https://app.codecov.io/gh/boostorg/math/pull/1192?src=pr&el=tree-more&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | |  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/math/pull/1192?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/math/pull/1192?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [ab57b20...d01893d](https://app.codecov.io/gh/boostorg/math/pull/1192?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---
