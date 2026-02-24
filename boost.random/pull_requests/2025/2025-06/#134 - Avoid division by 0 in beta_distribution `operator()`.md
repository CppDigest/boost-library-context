# #134 Avoid division by 0 in beta_distribution `operator()` [Merged]

> Username: mborland  
> Created at: 2025-06-30 15:20:59 UTC  
> Updated at: 2025-06-30 17:42:12 UTC  
> Merged at: 2025-06-30 17:41:39 UTC  
> Closed at: 2025-06-30 17:41:40 UTC  
> Url: https://github.com/boostorg/random/pull/134  

Closes: #133

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2025-06-30 15:58:17 UTC  
> Updated_at: 2025-06-30 17:42:12 UTC  
> Url: https://github.com/boostorg/random/pull/134#issuecomment-3019758143  

## [Codecov](https://app.codecov.io/gh/boostorg/random/pull/134?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
All modified and coverable lines are covered by tests :white_check_mark:  
> Project coverage is 95.85%. Comparing base [(`59a5eb8`)](https://app.codecov.io/gh/boostorg/random/commit/59a5eb88ada5311c4916270555360c8e3aeb7211?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) to head [(`c98dc05`)](https://app.codecov.io/gh/boostorg/random/commit/c98dc0555087be72ab1145ed14e0c6306c8a7c85?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> Report is 3 commits behind head on develop.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/random/pull/134/graphs/tree.svg?width=650&height=150&src=pr&token=7wvW4uAjLp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/random/pull/134?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #134      +/-   ##  
===========================================  
+ Coverage    95.83%   95.85%   +0.01%       
===========================================  
  Files          114      115       +1       
  Lines         8115     8126      +11       
===========================================  
+ Hits          7777     7789      +12       
+ Misses         338      337       -1       
```  
  
| [Files with missing lines](https://app.codecov.io/gh/boostorg/random/pull/134?dropdown=coverage&src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/random/beta\_distribution.hpp](https://app.codecov.io/gh/boostorg/random/pull/134?src=pr&el=tree&filepath=include%2Fboost%2Frandom%2Fbeta_distribution.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9yYW5kb20vYmV0YV9kaXN0cmlidXRpb24uaHBw) | `100.00% <100.00%> (ø)` | |  
| [test/github\_issue\_133.cpp](https://app.codecov.io/gh/boostorg/random/pull/134?src=pr&el=tree&filepath=test%2Fgithub_issue_133.cpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC9naXRodWJfaXNzdWVfMTMzLmNwcA==) | `100.00% <100.00%> (ø)` | |  
  
... and [1 file with indirect coverage changes](https://app.codecov.io/gh/boostorg/random/pull/134/indirect-changes?src=pr&el=tree-more&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/random/pull/134?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/random/pull/134?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [59a5eb8...c98dc05](https://app.codecov.io/gh/boostorg/random/pull/134?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>  
<details><summary> :rocket: New features to boost your workflow: </summary>  
  
- :snowflake: [Test Analytics](https://docs.codecov.com/docs/test-analytics): Detect flaky tests, report on failures, and find test suite problems.  
</details>

---

## Comment 2

> Username: mborland  
> Created_at: 2025-06-30 17:41:34 UTC  
> Url: https://github.com/boostorg/random/pull/134#issuecomment-3020148247  

The loop here keeps the shape of the distribution intact. I tried a simpler `if (a == 0) return 0`, but that skewed the distribution left.

---
