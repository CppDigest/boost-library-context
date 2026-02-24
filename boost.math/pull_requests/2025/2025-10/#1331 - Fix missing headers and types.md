# #1331 Fix missing headers and types [Merged]

> Username: mborland  
> Created at: 2025-10-23 11:27:51 UTC  
> Updated at: 2025-10-23 18:20:14 UTC  
> Merged at: 2025-10-23 13:24:50 UTC  
> Closed at: 2025-10-23 13:24:50 UTC  
> Url: https://github.com/boostorg/math/pull/1331  

The new version of the windows GCC runner does not have the `<cstdint>` types and header automatically injected into the global namespace (I don't think this was ever guaranteed either way). Add the headers and appropriate namespaces in bulk.  
  
FYI @NAThompson

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2025-10-23 12:44:09 UTC  
> Updated_at: 2025-10-23 18:20:14 UTC  
> Url: https://github.com/boostorg/math/pull/1331#issuecomment-3436728974  

## [Codecov](https://app.codecov.io/gh/boostorg/math/pull/1331?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
:x: Patch coverage is `97.72727%` with `1 line` in your changes missing coverage. Please review.  
:white_check_mark: Project coverage is 95.24%. Comparing base ([`975672b`](https://app.codecov.io/gh/boostorg/math/commit/975672b7b15ed3a42dfa6e0ac33b5bb3b897cadf?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)) to head ([`863c7eb`](https://app.codecov.io/gh/boostorg/math/commit/863c7ebc4e78936b7a740897cb8cdf64c06ed556?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)).  
:warning: Report is 3 commits behind head on develop.  
  
| [Files with missing lines](https://app.codecov.io/gh/boostorg/math/pull/1331?dropdown=coverage&src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Patch % | Lines |  
|---|---|---|  
| [...nclude/boost/math/quadrature/naive\_monte\_carlo.hpp](https://app.codecov.io/gh/boostorg/math/pull/1331?src=pr&el=tree&filepath=include%2Fboost%2Fmath%2Fquadrature%2Fnaive_monte_carlo.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9tYXRoL3F1YWRyYXR1cmUvbmFpdmVfbW9udGVfY2FybG8uaHBw) | 95.83% | [1 Missing :warning: ](https://app.codecov.io/gh/boostorg/math/pull/1331?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) |  
  
<details><summary>Additional details and impacted files</summary>  
  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/math/pull/1331/graphs/tree.svg?width=650&height=150&src=pr&token=26IDb1OAa7&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/math/pull/1331?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@           Coverage Diff            @@  
##           develop    #1331   +/-   ##  
========================================  
  Coverage    95.24%   95.24%             
========================================  
  Files          814      814             
  Lines        69309    69309             
========================================  
  Hits         66015    66015             
  Misses        3294     3294             
```  
  
| [Files with missing lines](https://app.codecov.io/gh/boostorg/math/pull/1331?dropdown=coverage&src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/math/interpolators/cubic\_hermite.hpp](https://app.codecov.io/gh/boostorg/math/pull/1331?src=pr&el=tree&filepath=include%2Fboost%2Fmath%2Finterpolators%2Fcubic_hermite.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9tYXRoL2ludGVycG9sYXRvcnMvY3ViaWNfaGVybWl0ZS5ocHA=) | `100.00% <ø> (ø)` | |  
| [...terpolators/detail/barycentric\_rational\_detail.hpp](https://app.codecov.io/gh/boostorg/math/pull/1331?src=pr&el=tree&filepath=include%2Fboost%2Fmath%2Finterpolators%2Fdetail%2Fbarycentric_rational_detail.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9tYXRoL2ludGVycG9sYXRvcnMvZGV0YWlsL2JhcnljZW50cmljX3JhdGlvbmFsX2RldGFpbC5ocHA=) | `82.71% <100.00%> (ø)` | |  
| [...lators/detail/cardinal\_quintic\_b\_spline\_detail.hpp](https://app.codecov.io/gh/boostorg/math/pull/1331?src=pr&el=tree&filepath=include%2Fboost%2Fmath%2Finterpolators%2Fdetail%2Fcardinal_quintic_b_spline_detail.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9tYXRoL2ludGVycG9sYXRvcnMvZGV0YWlsL2NhcmRpbmFsX3F1aW50aWNfYl9zcGxpbmVfZGV0YWlsLmhwcA==) | `93.65% <100.00%> (ø)` | |  
| [...math/interpolators/detail/cubic\_hermite\_detail.hpp](https://app.codecov.io/gh/boostorg/math/pull/1331?src=pr&el=tree&filepath=include%2Fboost%2Fmath%2Finterpolators%2Fdetail%2Fcubic_hermite_detail.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9tYXRoL2ludGVycG9sYXRvcnMvZGV0YWlsL2N1YmljX2hlcm1pdGVfZGV0YWlsLmhwcA==) | `74.12% <100.00%> (ø)` | |  
| [...th/interpolators/detail/quintic\_hermite\_detail.hpp](https://app.codecov.io/gh/boostorg/math/pull/1331?src=pr&el=tree&filepath=include%2Fboost%2Fmath%2Finterpolators%2Fdetail%2Fquintic_hermite_detail.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9tYXRoL2ludGVycG9sYXRvcnMvZGV0YWlsL3F1aW50aWNfaGVybWl0ZV9kZXRhaWwuaHBw) | `78.64% <100.00%> (ø)` | |  
| [...ath/interpolators/detail/septic\_hermite\_detail.hpp](https://app.codecov.io/gh/boostorg/math/pull/1331?src=pr&el=tree&filepath=include%2Fboost%2Fmath%2Finterpolators%2Fdetail%2Fseptic_hermite_detail.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9tYXRoL2ludGVycG9sYXRvcnMvZGV0YWlsL3NlcHRpY19oZXJtaXRlX2RldGFpbC5ocHA=) | `83.60% <100.00%> (ø)` | |  
| [...clude/boost/math/interpolators/quintic\_hermite.hpp](https://app.codecov.io/gh/boostorg/math/pull/1331?src=pr&el=tree&filepath=include%2Fboost%2Fmath%2Finterpolators%2Fquintic_hermite.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9tYXRoL2ludGVycG9sYXRvcnMvcXVpbnRpY19oZXJtaXRlLmhwcA==) | `100.00% <ø> (ø)` | |  
| [...nclude/boost/math/interpolators/septic\_hermite.hpp](https://app.codecov.io/gh/boostorg/math/pull/1331?src=pr&el=tree&filepath=include%2Fboost%2Fmath%2Finterpolators%2Fseptic_hermite.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9tYXRoL2ludGVycG9sYXRvcnMvc2VwdGljX2hlcm1pdGUuaHBw) | `100.00% <ø> (ø)` | |  
| [...nclude/boost/math/quadrature/naive\_monte\_carlo.hpp](https://app.codecov.io/gh/boostorg/math/pull/1331?src=pr&el=tree&filepath=include%2Fboost%2Fmath%2Fquadrature%2Fnaive_monte_carlo.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9tYXRoL3F1YWRyYXR1cmUvbmFpdmVfbW9udGVfY2FybG8uaHBw) | `82.74% <95.83%> (ø)` | |  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/math/pull/1331?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/math/pull/1331?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [975672b...863c7eb](https://app.codecov.io/gh/boostorg/math/pull/1331?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
</details>  
<details><summary> :rocket: New features to boost your workflow: </summary>  
  
- :snowflake: [Test Analytics](https://docs.codecov.com/docs/test-analytics): Detect flaky tests, report on failures, and find test suite problems.  
</details>

---
