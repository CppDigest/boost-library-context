# #663 Don't use deprecated form of user-defined literal operator [Merged]

> Username: Thomas-Barbier-1A  
> Created at: 2025-03-17 13:56:42 UTC  
> Updated at: 2025-03-17 20:23:46 UTC  
> Merged at: 2025-03-17 20:20:44 UTC  
> Closed at: 2025-03-17 20:20:44 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/663  



---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2025-03-17 17:44:22 UTC  
> Updated_at: 2025-03-17 20:23:45 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/663#issuecomment-2730374681  

## [Codecov](https://app.codecov.io/gh/boostorg/multiprecision/pull/663?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
All modified and coverable lines are covered by tests :white_check_mark:  
> Project coverage is 94.1%. Comparing base [(`49ef290`)](https://app.codecov.io/gh/boostorg/multiprecision/commit/49ef290b0c22b717f9158dd000c1cad8b5e43be5?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) to head [(`ade2b95`)](https://app.codecov.io/gh/boostorg/multiprecision/commit/ade2b95d373e8030010a108fcd741746eb12084d?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> Report is 2 commits behind head on develop.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/multiprecision/pull/663/graphs/tree.svg?width=650&height=150&src=pr&token=SDDBym7Pc9&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/multiprecision/pull/663?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@            Coverage Diff            @@  
##           develop    #663     +/-   ##  
=========================================  
- Coverage     94.1%   94.1%   -0.0%       
=========================================  
  Files          279     279               
  Lines        28979   28979               
=========================================  
- Hits         27255   27253      -2       
- Misses        1724    1726      +2       
```  
  
| [Files with missing lines](https://app.codecov.io/gh/boostorg/multiprecision/pull/663?dropdown=coverage&src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/multiprecision/cpp\_int/literals.hpp](https://app.codecov.io/gh/boostorg/multiprecision/pull/663?src=pr&el=tree&filepath=include%2Fboost%2Fmultiprecision%2Fcpp_int%2Fliterals.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9tdWx0aXByZWNpc2lvbi9jcHBfaW50L2xpdGVyYWxzLmhwcA==) | `100.0% <ø> (ø)` | |  
  
... and [1 file with indirect coverage changes](https://app.codecov.io/gh/boostorg/multiprecision/pull/663/indirect-changes?src=pr&el=tree-more&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/multiprecision/pull/663?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/multiprecision/pull/663?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [49ef290...ade2b95](https://app.codecov.io/gh/boostorg/multiprecision/pull/663?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>  
<details><summary>🚀 New features to boost your workflow: </summary>  
  
- ❄ [Test Analytics](https://docs.codecov.com/docs/test-analytics): Detect flaky tests, report on failures, and find test suite problems.  
</details>

---

## Comment 2

> Username: ckormanyos  
> Created_at: 2025-03-17 20:16:51 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/663#issuecomment-2730735646  

Thanks @Thomas-Barbier-1A for catching this and @mborland for the quick fix for the next release!

---
