# #669 Simplify BOOST_MP_DEFINE_SIZED_CPP_INT_LITERAL [Closed]

> Username: pps83  
> Created at: 2025-03-25 18:40:00 UTC  
> Updated at: 2025-03-25 20:38:37 UTC  
> Closed at: 2025-03-25 18:45:47 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/669  

remove unnecessary BOOST_JOIN

---

## Comment 1

> Username: pps83  
> Created_at: 2025-03-25 18:46:05 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/669#issuecomment-2752217962  

vs complains with this one

---

## Comment 2

> Username: codecov[bot]  
> Created_at: 2025-03-25 19:35:24 UTC  
> Updated_at: 2025-03-25 19:57:38 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/669#issuecomment-2752328068  

## [Codecov](https://app.codecov.io/gh/boostorg/multiprecision/pull/669?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
All modified and coverable lines are covered by tests :white_check_mark:  
> Project coverage is 94.1%. Comparing base [(`d927981`)](https://app.codecov.io/gh/boostorg/multiprecision/commit/d9279817061b58b9201ef74980307fcb9a2089f6?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) to head [(`a740323`)](https://app.codecov.io/gh/boostorg/multiprecision/commit/a740323afee7124aa9802c376f95e4f5b1762bef?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> Report is 1 commits behind head on develop.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/multiprecision/pull/669/graphs/tree.svg?width=650&height=150&src=pr&token=SDDBym7Pc9&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/multiprecision/pull/669?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@           Coverage Diff           @@  
##           develop    #669   +/-   ##  
=======================================  
  Coverage     94.1%   94.1%             
=======================================  
  Files          279     279             
  Lines        28979   28979             
=======================================  
  Hits         27253   27253             
  Misses        1726    1726             
```  
  
| [Files with missing lines](https://app.codecov.io/gh/boostorg/multiprecision/pull/669?dropdown=coverage&src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/multiprecision/cpp\_int/literals.hpp](https://app.codecov.io/gh/boostorg/multiprecision/pull/669?src=pr&el=tree&filepath=include%2Fboost%2Fmultiprecision%2Fcpp_int%2Fliterals.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9tdWx0aXByZWNpc2lvbi9jcHBfaW50L2xpdGVyYWxzLmhwcA==) | `100.0% <ø> (ø)` | |  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/multiprecision/pull/669?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/multiprecision/pull/669?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [d927981...a740323](https://app.codecov.io/gh/boostorg/multiprecision/pull/669?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>  
<details><summary> :rocket: New features to boost your workflow: </summary>  
  
- :snowflake: [Test Analytics](https://docs.codecov.com/docs/test-analytics): Detect flaky tests, report on failures, and find test suite problems.  
</details>

---
