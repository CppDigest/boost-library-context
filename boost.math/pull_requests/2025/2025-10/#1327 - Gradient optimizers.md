# #1327 Gradient optimizers [Closed]

> Username: demroz  
> Created at: 2025-10-17 17:06:23 UTC  
> Updated at: 2025-10-18 01:43:56 UTC  
> Closed at: 2025-10-17 19:33:38 UTC  
> Url: https://github.com/boostorg/math/pull/1327  

- adds a few gradient optimizers  
- gradient descent, nesterov, and L-BFGS  
  
everything is policy based, so these should be quite extensible. I may add a few more optimziers in the future. Also although everything is reverse-mode autodiff centric, as long as you provide the objective function, a way to evaluate it, and a way to evaluate the gradient, everything should work correctly.  
  
For some examples on how to use the optimziers: `test_gradient_descent_optimizer.cpp`, `test_nesterov_optimizer.cpp`, and `test_lbfgs.cpp` should be good starting points.  
  
I'm working on the documentation currently. I wanted to hold off to see if any major revisions are necessary

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2025-10-17 21:55:58 UTC  
> Updated_at: 2025-10-18 01:43:56 UTC  
> Url: https://github.com/boostorg/math/pull/1327#issuecomment-3417368544  

## [Codecov](https://app.codecov.io/gh/boostorg/math/pull/1327?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
:x: Patch coverage is `96.32353%` with `5 lines` in your changes missing coverage. Please review.  
:white_check_mark: Project coverage is 95.24%. Comparing base ([`2697069`](https://app.codecov.io/gh/boostorg/math/commit/26970693298662419eb109c8c975dcdbd654da5a?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)) to head ([`b30a557`](https://app.codecov.io/gh/boostorg/math/commit/b30a5577cf1251e4362c8d2687c6822ee7dc611f?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)).  
:warning: Report is 11 commits behind head on develop.  
  
| [Files with missing lines](https://app.codecov.io/gh/boostorg/math/pull/1327?dropdown=coverage&src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Patch % | Lines |  
|---|---|---|  
| [...detail/reverse\_mode\_autodiff\_memory\_management.hpp](https://app.codecov.io/gh/boostorg/math/pull/1327?src=pr&el=tree&filepath=include%2Fboost%2Fmath%2Fdifferentiation%2Fdetail%2Freverse_mode_autodiff_memory_management.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9tYXRoL2RpZmZlcmVudGlhdGlvbi9kZXRhaWwvcmV2ZXJzZV9tb2RlX2F1dG9kaWZmX21lbW9yeV9tYW5hZ2VtZW50LmhwcA==) | 95.00% | [5 Missing :warning: ](https://app.codecov.io/gh/boostorg/math/pull/1327?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) |  
  
<details><summary>Additional details and impacted files</summary>  
  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/math/pull/1327/graphs/tree.svg?width=650&height=150&src=pr&token=26IDb1OAa7&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/math/pull/1327?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@           Coverage Diff            @@  
##           develop    #1327   +/-   ##  
========================================  
  Coverage    95.24%   95.24%             
========================================  
  Files          814      814             
  Lines        69309    69311    +2       
========================================  
+ Hits         66015    66017    +2       
  Misses        3294     3294             
```  
  
| [Files with missing lines](https://app.codecov.io/gh/boostorg/math/pull/1327?dropdown=coverage&src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [...de/boost/math/differentiation/autodiff\_reverse.hpp](https://app.codecov.io/gh/boostorg/math/pull/1327?src=pr&el=tree&filepath=include%2Fboost%2Fmath%2Fdifferentiation%2Fautodiff_reverse.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9tYXRoL2RpZmZlcmVudGlhdGlvbi9hdXRvZGlmZl9yZXZlcnNlLmhwcA==) | `98.78% <ø> (ø)` | |  
| [test/test\_functions\_for\_optimization.hpp](https://app.codecov.io/gh/boostorg/math/pull/1327?src=pr&el=tree&filepath=test%2Ftest_functions_for_optimization.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC90ZXN0X2Z1bmN0aW9uc19mb3Jfb3B0aW1pemF0aW9uLmhwcA==) | `100.00% <100.00%> (ø)` | |  
| [...detail/reverse\_mode\_autodiff\_memory\_management.hpp](https://app.codecov.io/gh/boostorg/math/pull/1327?src=pr&el=tree&filepath=include%2Fboost%2Fmath%2Fdifferentiation%2Fdetail%2Freverse_mode_autodiff_memory_management.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9tYXRoL2RpZmZlcmVudGlhdGlvbi9kZXRhaWwvcmV2ZXJzZV9tb2RlX2F1dG9kaWZmX21lbW9yeV9tYW5hZ2VtZW50LmhwcA==) | `95.00% <95.00%> (+0.10%)` | :arrow_up: |  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/math/pull/1327?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/math/pull/1327?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [2697069...b30a557](https://app.codecov.io/gh/boostorg/math/pull/1327?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
</details>  
<details><summary> :rocket: New features to boost your workflow: </summary>  
  
- :snowflake: [Test Analytics](https://docs.codecov.com/docs/test-analytics): Detect flaky tests, report on failures, and find test suite problems.  
</details>

---
