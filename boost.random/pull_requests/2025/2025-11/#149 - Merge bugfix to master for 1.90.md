# #149 Merge bugfix to master for 1.90 [Merged]

> Username: mborland  
> Created at: 2025-11-12 10:26:18 UTC  
> Updated at: 2025-11-14 14:34:06 UTC  
> Merged at: 2025-11-14 14:33:03 UTC  
> Closed at: 2025-11-14 14:33:03 UTC  
> Url: https://github.com/boostorg/random/pull/149  



---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2025-11-12 10:45:30 UTC  
> Updated_at: 2025-11-14 14:34:06 UTC  
> Url: https://github.com/boostorg/random/pull/149#issuecomment-3521284120  

## [Codecov](https://app.codecov.io/gh/boostorg/random/pull/149?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
:x: Patch coverage is `95.45455%` with `1 line` in your changes missing coverage. Please review.  
:white_check_mark: Project coverage is 95.79%. Comparing base ([`49d131b`](https://app.codecov.io/gh/boostorg/random/commit/49d131b32868109804491afeda8202507fd349f2?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)) to head ([`525a07c`](https://app.codecov.io/gh/boostorg/random/commit/525a07cb70b53b14b707669308fcc96dee938713?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)).  
:warning: Report is 14 commits behind head on master.  
  
| [Files with missing lines](https://app.codecov.io/gh/boostorg/random/pull/149?dropdown=coverage&src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Patch % | Lines |  
|---|---|---|  
| [test/github\_issue\_147.cpp](https://app.codecov.io/gh/boostorg/random/pull/149?src=pr&el=tree&filepath=test%2Fgithub_issue_147.cpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC9naXRodWJfaXNzdWVfMTQ3LmNwcA==) | 94.73% | [1 Missing :warning: ](https://app.codecov.io/gh/boostorg/random/pull/149?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) |  
  
<details><summary>Additional details and impacted files</summary>  
  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/random/pull/149/graphs/tree.svg?width=650&height=150&src=pr&token=7wvW4uAjLp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/random/pull/149?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@            Coverage Diff             @@  
##           master     #149      +/-   ##  
==========================================  
- Coverage   95.79%   95.79%   -0.01%       
==========================================  
  Files         115      116       +1       
  Lines        7640     7659      +19       
==========================================  
+ Hits         7319     7337      +18       
- Misses        321      322       +1       
```  
  
| [Files with missing lines](https://app.codecov.io/gh/boostorg/random/pull/149?dropdown=coverage&src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/random/detail/xoshiro\_base.hpp](https://app.codecov.io/gh/boostorg/random/pull/149?src=pr&el=tree&filepath=include%2Fboost%2Frandom%2Fdetail%2Fxoshiro_base.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9yYW5kb20vZGV0YWlsL3hvc2hpcm9fYmFzZS5ocHA=) | `100.00% <100.00%> (ø)` | |  
| [test/github\_issue\_147.cpp](https://app.codecov.io/gh/boostorg/random/pull/149?src=pr&el=tree&filepath=test%2Fgithub_issue_147.cpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC9naXRodWJfaXNzdWVfMTQ3LmNwcA==) | `94.73% <94.73%> (ø)` | |  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/random/pull/149?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/random/pull/149?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [49d131b...525a07c](https://app.codecov.io/gh/boostorg/random/pull/149?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
</details>  
<details><summary> :rocket: New features to boost your workflow: </summary>  
  
- :snowflake: [Test Analytics](https://docs.codecov.com/docs/test-analytics): Detect flaky tests, report on failures, and find test suite problems.  
</details>

---
