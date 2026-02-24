# #689 Revert recent changes in cpp_int/bitwise [Closed]

> Username: ckormanyos  
> Created at: 2025-05-19 11:29:38 UTC  
> Updated at: 2025-05-26 07:40:34 UTC  
> Closed at: 2025-05-26 07:39:01 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/689  

The purpose of this PR is to revert recent changes in `cpp_int/bitwise.hpp` in order to see if these are the cause of the undefined behavior detections in an [unrelated Math PR](https://github.com/boostorg/math/pull/1251#issuecomment-2889048121).  
  
Cc: @mborland and @jzmaddock

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2025-05-19 12:04:17 UTC  
> Updated_at: 2025-05-26 07:39:21 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/689#issuecomment-2890759902  

## [Codecov](https://app.codecov.io/gh/boostorg/multiprecision/pull/689?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
All modified and coverable lines are covered by tests :white_check_mark:  
> Project coverage is 94.1%. Comparing base [(`9de48c7`)](https://app.codecov.io/gh/boostorg/multiprecision/commit/9de48c778386076a7261554032a41de222349fa7?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) to head [(`ccdaf10`)](https://app.codecov.io/gh/boostorg/multiprecision/commit/ccdaf10b81578a86a36ef158062d2f2df4a9d8e8?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/multiprecision/pull/689/graphs/tree.svg?width=650&height=150&src=pr&token=SDDBym7Pc9&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/multiprecision/pull/689?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@            Coverage Diff            @@  
##           develop    #689     +/-   ##  
=========================================  
- Coverage     94.1%   94.1%   -0.0%       
=========================================  
  Files          280     280               
  Lines        29074   29074               
=========================================  
- Hits         27349   27347      -2       
- Misses        1725    1727      +2       
```  
  
| [Files with missing lines](https://app.codecov.io/gh/boostorg/multiprecision/pull/689?dropdown=coverage&src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/multiprecision/cpp\_int/bitwise.hpp](https://app.codecov.io/gh/boostorg/multiprecision/pull/689?src=pr&el=tree&filepath=include%2Fboost%2Fmultiprecision%2Fcpp_int%2Fbitwise.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9tdWx0aXByZWNpc2lvbi9jcHBfaW50L2JpdHdpc2UuaHBw) | `85.2% <100.0%> (ø)` | |  
  
... and [1 file with indirect coverage changes](https://app.codecov.io/gh/boostorg/multiprecision/pull/689/indirect-changes?src=pr&el=tree-more&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/multiprecision/pull/689?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/multiprecision/pull/689?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [9de48c7...ccdaf10](https://app.codecov.io/gh/boostorg/multiprecision/pull/689?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>  
<details><summary> :rocket: New features to boost your workflow: </summary>  
  
- :snowflake: [Test Analytics](https://docs.codecov.com/docs/test-analytics): Detect flaky tests, report on failures, and find test suite problems.  
</details>

---

## Comment 2

> Username: ckormanyos  
> Created_at: 2025-05-26 07:39:01 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/689#issuecomment-2908854194  

Closing since this is not needed

---
