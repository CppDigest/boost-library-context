# #737 Refactor loop for warning issue 733 [Merged]

> Username: ckormanyos  
> Created at: 2025-09-08 09:31:22 UTC  
> Updated at: 2025-09-08 16:19:13 UTC  
> Merged at: 2025-09-08 10:50:49 UTC  
> Closed at: 2025-09-08 10:50:49 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/737  

Intended to handle #733

---

## Comment 1

> Username: ckormanyos  
> Created_at: 2025-09-08 10:12:52 UTC  
> Updated_at: 2025-09-08 10:13:17 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/737#issuecomment-3265586876  

Tested locally with:  
  
```  
g++ -std=c++20 -O3 -Wall -Wextra -Wpedantic -Wconversion -Wsign-conversion -Wshadow -Wuninitialized -Wunused-parameter -Wunreachable-code -Wswitch-default -Winit-self -Wundef  
```

---

## Comment 2

> Username: codecov[bot]  
> Created_at: 2025-09-08 10:25:18 UTC  
> Updated_at: 2025-09-08 16:19:13 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/737#issuecomment-3265637972  

## [Codecov](https://app.codecov.io/gh/boostorg/multiprecision/pull/737?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
:white_check_mark: All modified and coverable lines are covered by tests.  
:white_check_mark: Project coverage is 96.2%. Comparing base ([`32fef7c`](https://app.codecov.io/gh/boostorg/multiprecision/commit/32fef7c689c3d7cb02f448e258e54fbcae2db300?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)) to head ([`0ddffc2`](https://app.codecov.io/gh/boostorg/multiprecision/commit/0ddffc25cc38d314b4ba03d84778e7352f8095b4?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)).  
:warning: Report is 2 commits behind head on develop.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/multiprecision/pull/737/graphs/tree.svg?width=650&height=150&src=pr&token=SDDBym7Pc9&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/multiprecision/pull/737?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@           Coverage Diff           @@  
##           develop    #737   +/-   ##  
=======================================  
  Coverage     96.2%   96.2%             
=======================================  
  Files          300     300             
  Lines        31349   31349             
=======================================  
  Hits         30139   30139             
  Misses        1210    1210             
```  
  
| [Files with missing lines](https://app.codecov.io/gh/boostorg/multiprecision/pull/737?dropdown=coverage&src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/multiprecision/cpp\_int/bitwise.hpp](https://app.codecov.io/gh/boostorg/multiprecision/pull/737?src=pr&el=tree&filepath=include%2Fboost%2Fmultiprecision%2Fcpp_int%2Fbitwise.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9tdWx0aXByZWNpc2lvbi9jcHBfaW50L2JpdHdpc2UuaHBw) | `85.2% <100.0%> (ø)` | |  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/multiprecision/pull/737?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/multiprecision/pull/737?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [32fef7c...0ddffc2](https://app.codecov.io/gh/boostorg/multiprecision/pull/737?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
</details>  
<details><summary> :rocket: New features to boost your workflow: </summary>  
  
- :snowflake: [Test Analytics](https://docs.codecov.com/docs/test-analytics): Detect flaky tests, report on failures, and find test suite problems.  
</details>

---
