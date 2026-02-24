# #1265 Adjust parameter order in Differential Evolution algorithm [Merged]

> Username: Tomato-in  
> Created at: 2025-05-15 08:21:10 UTC  
> Updated at: 2025-05-15 14:28:24 UTC  
> Merged at: 2025-05-15 14:19:59 UTC  
> Closed at: 2025-05-15 14:19:59 UTC  
> Url: https://github.com/boostorg/math/pull/1265  

### Problem Description  
The optimization module implements four algorithms: Differential Evolution, Algorithm jSO, Random Search, and Evolution Strategy with Covariance Matrix Adaptation. All these algorithms accept seven parameters: `cost_function`, `params`, `gen`, `value_to_reach`, `cancellation`, `current_minimum_cost`, and `queries`. Unfortunately, the Differential Evolution algorithm doesn't maintain parameter consistency with the other three algorithms: the last two parameters (`current_minimum_cost` and `queries`) are in reverse order compared to the other algorithms. This appears to be an unintentional oversight.  
  
### Solution  
- Adjust the parameter order in the Differential Evolution algorithm to ensure consistency with other optimization algorithms;  
- Update the algorithm documentation accordingly;  
- No changes to example code were needed as the examples don't use these two parameters.

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2025-05-15 09:02:50 UTC  
> Updated_at: 2025-05-15 14:28:23 UTC  
> Url: https://github.com/boostorg/math/pull/1265#issuecomment-2883096702  

## [Codecov](https://app.codecov.io/gh/boostorg/math/pull/1265?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
All modified and coverable lines are covered by tests :white_check_mark:  
> Project coverage is 93.91%. Comparing base [(`6bd0191`)](https://app.codecov.io/gh/boostorg/math/commit/6bd0191186d2a3441b4d258ae57a8523b6bbce85?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) to head [(`7d50747`)](https://app.codecov.io/gh/boostorg/math/commit/7d507477d1751fcd0caec28e504fe64ca1026a55?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> Report is 1 commits behind head on develop.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/math/pull/1265/graphs/tree.svg?width=650&height=150&src=pr&token=26IDb1OAa7&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/math/pull/1265?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@           Coverage Diff            @@  
##           develop    #1265   +/-   ##  
========================================  
  Coverage    93.91%   93.91%             
========================================  
  Files          661      661             
  Lines        54866    54866             
========================================  
  Hits         51526    51526             
  Misses        3340     3340             
```  
  
| [Files with missing lines](https://app.codecov.io/gh/boostorg/math/pull/1265?dropdown=coverage&src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [...boost/math/optimization/differential\_evolution.hpp](https://app.codecov.io/gh/boostorg/math/pull/1265?src=pr&el=tree&filepath=include%2Fboost%2Fmath%2Foptimization%2Fdifferential_evolution.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9tYXRoL29wdGltaXphdGlvbi9kaWZmZXJlbnRpYWxfZXZvbHV0aW9uLmhwcA==) | `82.40% <ø> (ø)` | |  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/math/pull/1265?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/math/pull/1265?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [6bd0191...7d50747](https://app.codecov.io/gh/boostorg/math/pull/1265?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>  
<details><summary> :rocket: New features to boost your workflow: </summary>  
  
- :snowflake: [Test Analytics](https://docs.codecov.com/docs/test-analytics): Detect flaky tests, report on failures, and find test suite problems.  
</details>

---

## Comment 2

> Username: ckormanyos  
> Created_at: 2025-05-15 09:05:42 UTC  
> Url: https://github.com/boostorg/math/pull/1265#issuecomment-2883104562  

Hi @Tomato-in for info, ... If I understand correctly, the `sycl` failures in CI are related to known problems and not within the scope of your changes.

---

## Comment 3

> Username: Tomato-in  
> Created_at: 2025-05-15 09:19:02 UTC  
> Url: https://github.com/boostorg/math/pull/1265#issuecomment-2883140741  

> Hi @Tomato-in for info, ... If I understand correctly, the `sycl` failures in CI are related to known problems and not within the scope of your changes.  
  
Thank you for confirming. I'm glad to hear the SYCL failures are unrelated to my changes.

---

## Comment 4

> Username: NAThompson  
> Created_at: 2025-05-15 14:19:41 UTC  
> Url: https://github.com/boostorg/math/pull/1265#issuecomment-2883990126  

@Tomato-in : Thanks! This indeed was a mistake on my part.

---
