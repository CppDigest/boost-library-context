# #1250   Fix lower incomplete gamma functions with x = 0 [Closed]

> Username: cohomology  
> Created at: 2025-04-01 19:32:23 UTC  
> Updated at: 2025-04-02 07:53:22 UTC  
> Closed at: 2025-04-02 07:53:11 UTC  
> Url: https://github.com/boostorg/math/pull/1250  

In this case, the errno error handling did not work correctly, as internal functions where accidently setting it, although no  
overflow happens.  
  
Fixes #1249.

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2025-04-01 20:49:34 UTC  
> Updated_at: 2025-04-02 07:53:20 UTC  
> Url: https://github.com/boostorg/math/pull/1250#issuecomment-2770651801  

## [Codecov](https://app.codecov.io/gh/boostorg/math/pull/1250?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
Attention: Patch coverage is `98.30508%` with `1 line` in your changes missing coverage. Please review.  
> Project coverage is 93.84%. Comparing base [(`a5c0625`)](https://app.codecov.io/gh/boostorg/math/commit/a5c062542afb2037054c4e1b1be44db7b0859c38?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) to head [(`6e5ba9a`)](https://app.codecov.io/gh/boostorg/math/commit/6e5ba9afbbabd275206a08f0126f6249ebe01886?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
| [Files with missing lines](https://app.codecov.io/gh/boostorg/math/pull/1250?dropdown=coverage&src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Patch % | Lines |  
|---|---|---|  
| [include/boost/math/special\_functions/gamma.hpp](https://app.codecov.io/gh/boostorg/math/pull/1250?src=pr&el=tree&filepath=include%2Fboost%2Fmath%2Fspecial_functions%2Fgamma.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9tYXRoL3NwZWNpYWxfZnVuY3Rpb25zL2dhbW1hLmhwcA==) | 90.90% | [1 Missing :warning: ](https://app.codecov.io/gh/boostorg/math/pull/1250?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) |  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/math/pull/1250/graphs/tree.svg?width=650&height=150&src=pr&token=26IDb1OAa7&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/math/pull/1250?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@           Coverage Diff            @@  
##           develop    #1250   +/-   ##  
========================================  
  Coverage    93.83%   93.84%             
========================================  
  Files          657      658    +1       
  Lines        55244    55298   +54       
========================================  
+ Hits         51840    51895   +55       
+ Misses        3404     3403    -1       
```  
  
| [Files with missing lines](https://app.codecov.io/gh/boostorg/math/pull/1250?dropdown=coverage&src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [test/git\_issue\_1249.cpp](https://app.codecov.io/gh/boostorg/math/pull/1250?src=pr&el=tree&filepath=test%2Fgit_issue_1249.cpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC9naXRfaXNzdWVfMTI0OS5jcHA=) | `100.00% <100.00%> (ø)` | |  
| [include/boost/math/special\_functions/gamma.hpp](https://app.codecov.io/gh/boostorg/math/pull/1250?src=pr&el=tree&filepath=include%2Fboost%2Fmath%2Fspecial_functions%2Fgamma.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9tYXRoL3NwZWNpYWxfZnVuY3Rpb25zL2dhbW1hLmhwcA==) | `92.18% <90.90%> (-0.08%)` | :arrow_down: |  
  
... and [1 file with indirect coverage changes](https://app.codecov.io/gh/boostorg/math/pull/1250/indirect-changes?src=pr&el=tree-more&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/math/pull/1250?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/math/pull/1250?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [a5c0625...6e5ba9a](https://app.codecov.io/gh/boostorg/math/pull/1250?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>  
<details><summary> :rocket: New features to boost your workflow: </summary>  
  
- :snowflake: [Test Analytics](https://docs.codecov.com/docs/test-analytics): Detect flaky tests, report on failures, and find test suite problems.  
</details>

---
