# #127 Remove Heavy Dependencies [Merged]

> Username: mborland  
> Created at: 2025-02-03 16:11:14 UTC  
> Updated at: 2025-02-03 18:33:09 UTC  
> Merged at: 2025-02-03 18:32:28 UTC  
> Closed at: 2025-02-03 18:32:28 UTC  
> Url: https://github.com/boostorg/random/pull/127  

See: https://pdimov.github.io/boostdep-report/develop/module-weights.html  
  
By dropping array and range our weight should come down considerably.

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2025-02-03 16:27:58 UTC  
> Updated_at: 2025-02-03 18:33:09 UTC  
> Url: https://github.com/boostorg/random/pull/127#issuecomment-2631485266  

## [Codecov](https://app.codecov.io/gh/boostorg/random/pull/127?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
All modified and coverable lines are covered by tests :white_check_mark:  
> Project coverage is 95.74%. Comparing base [(`4a4cdfe`)](https://app.codecov.io/gh/boostorg/random/commit/4a4cdfe410a4afd0b97b8a7956e31825a2cd2978?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) to head [(`efb6d03`)](https://app.codecov.io/gh/boostorg/random/commit/efb6d03ed42bb27986097ad39cf34ccf3b7754b9?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> Report is 5 commits behind head on develop.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/random/pull/127/graphs/tree.svg?width=650&height=150&src=pr&token=7wvW4uAjLp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/random/pull/127?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@           Coverage Diff            @@  
##           develop     #127   +/-   ##  
========================================  
  Coverage    95.74%   95.74%             
========================================  
  Files          112      112             
  Lines         7941     7941             
========================================  
  Hits          7603     7603             
  Misses         338      338             
```  
  
| [Files with missing lines](https://app.codecov.io/gh/boostorg/random/pull/127?dropdown=coverage&src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/random/discrete\_distribution.hpp](https://app.codecov.io/gh/boostorg/random/pull/127?src=pr&el=tree&filepath=include%2Fboost%2Frandom%2Fdiscrete_distribution.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9yYW5kb20vZGlzY3JldGVfZGlzdHJpYnV0aW9uLmhwcA==) | `97.57% <100.00%> (ø)` | |  
| [...ude/boost/random/hyperexponential\_distribution.hpp](https://app.codecov.io/gh/boostorg/random/pull/127?src=pr&el=tree&filepath=include%2Fboost%2Frandom%2Fhyperexponential_distribution.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9yYW5kb20vaHlwZXJleHBvbmVudGlhbF9kaXN0cmlidXRpb24uaHBw) | `95.42% <100.00%> (ø)` | |  
| [include/boost/random/mixmax.hpp](https://app.codecov.io/gh/boostorg/random/pull/127?src=pr&el=tree&filepath=include%2Fboost%2Frandom%2Fmixmax.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9yYW5kb20vbWl4bWF4LmhwcA==) | `99.11% <100.00%> (ø)` | |  
| [...e/boost/random/piecewise\_constant\_distribution.hpp](https://app.codecov.io/gh/boostorg/random/pull/127?src=pr&el=tree&filepath=include%2Fboost%2Frandom%2Fpiecewise_constant_distribution.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9yYW5kb20vcGllY2V3aXNlX2NvbnN0YW50X2Rpc3RyaWJ1dGlvbi5ocHA=) | `99.39% <100.00%> (ø)` | |  
| [...ude/boost/random/piecewise\_linear\_distribution.hpp](https://app.codecov.io/gh/boostorg/random/pull/127?src=pr&el=tree&filepath=include%2Fboost%2Frandom%2Fpiecewise_linear_distribution.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9yYW5kb20vcGllY2V3aXNlX2xpbmVhcl9kaXN0cmlidXRpb24uaHBw) | `99.05% <100.00%> (ø)` | |  
| [include/boost/random/seed\_seq.hpp](https://app.codecov.io/gh/boostorg/random/pull/127?src=pr&el=tree&filepath=include%2Fboost%2Frandom%2Fseed_seq.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9yYW5kb20vc2VlZF9zZXEuaHBw) | `100.00% <100.00%> (ø)` | |  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/random/pull/127?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/random/pull/127?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [4a4cdfe...efb6d03](https://app.codecov.io/gh/boostorg/random/pull/127?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---
