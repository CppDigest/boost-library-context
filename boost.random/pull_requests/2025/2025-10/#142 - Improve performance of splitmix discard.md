# #142 Improve performance of splitmix discard [Merged]

> Username: mborland  
> Created at: 2025-10-24 04:21:37 UTC  
> Updated at: 2025-10-24 06:40:15 UTC  
> Merged at: 2025-10-24 06:40:06 UTC  
> Closed at: 2025-10-24 06:40:06 UTC  
> Url: https://github.com/boostorg/random/pull/142  

Closes: #141

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2025-10-24 04:40:49 UTC  
> Updated_at: 2025-10-24 06:40:15 UTC  
> Url: https://github.com/boostorg/random/pull/142#issuecomment-3441033731  

## [Codecov](https://app.codecov.io/gh/boostorg/random/pull/142?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
:white_check_mark: All modified and coverable lines are covered by tests.  
:white_check_mark: Project coverage is 95.79%. Comparing base ([`be37499`](https://app.codecov.io/gh/boostorg/random/commit/be37499139d93d5f928bd5a97562346a92100847?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)) to head ([`852cb0e`](https://app.codecov.io/gh/boostorg/random/commit/852cb0e37b325ca9e0e668d37f4e2797c4512c1f?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)).  
:warning: Report is 2 commits behind head on develop.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/random/pull/142/graphs/tree.svg?width=650&height=150&src=pr&token=7wvW4uAjLp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/random/pull/142?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #142      +/-   ##  
===========================================  
- Coverage    95.85%   95.79%   -0.06%       
===========================================  
  Files          115      115                
  Lines         8128     7642     -486       
===========================================  
- Hits          7791     7321     -470       
+ Misses         337      321      -16       
```  
  
| [Files with missing lines](https://app.codecov.io/gh/boostorg/random/pull/142?dropdown=coverage&src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/random/splitmix64.hpp](https://app.codecov.io/gh/boostorg/random/pull/142?src=pr&el=tree&filepath=include%2Fboost%2Frandom%2Fsplitmix64.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9yYW5kb20vc3BsaXRtaXg2NC5ocHA=) | `100.00% <100.00%> (ø)` | |  
  
... and [92 files with indirect coverage changes](https://app.codecov.io/gh/boostorg/random/pull/142/indirect-changes?src=pr&el=tree-more&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/random/pull/142?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/random/pull/142?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [be37499...852cb0e](https://app.codecov.io/gh/boostorg/random/pull/142?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
</details>  
<details><summary> :rocket: New features to boost your workflow: </summary>  
  
- :snowflake: [Test Analytics](https://docs.codecov.com/docs/test-analytics): Detect flaky tests, report on failures, and find test suite problems.  
</details>

---
