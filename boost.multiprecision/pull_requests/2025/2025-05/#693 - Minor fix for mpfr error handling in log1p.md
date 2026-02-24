# #693 Minor fix for mpfr error handling in log1p. [Merged]

> Username: jzmaddock  
> Created at: 2025-05-28 15:17:01 UTC  
> Updated at: 2025-05-28 21:55:59 UTC  
> Merged at: 2025-05-28 16:20:05 UTC  
> Closed at: 2025-05-28 16:20:05 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/693  

This brings the mpfr version of the function into line with Boost.Math's improved coverage tests and changes an evaluation_error to a domain_error for x < -1.

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2025-05-28 15:57:21 UTC  
> Updated_at: 2025-05-28 21:55:57 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/693#issuecomment-2916851866  

## [Codecov](https://app.codecov.io/gh/boostorg/multiprecision/pull/693?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
All modified and coverable lines are covered by tests :white_check_mark:  
> Project coverage is 94.1%. Comparing base [(`a7094d2`)](https://app.codecov.io/gh/boostorg/multiprecision/commit/a7094d2a5ac1837d239e8d70bb40493dc5fb2ec2?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) to head [(`55c7b7c`)](https://app.codecov.io/gh/boostorg/multiprecision/commit/55c7b7ca1ac16d54589de907de9882ba2a813404?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> Report is 2 commits behind head on develop.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/multiprecision/pull/693/graphs/tree.svg?width=650&height=150&src=pr&token=SDDBym7Pc9&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/multiprecision/pull/693?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@           Coverage Diff           @@  
##           develop    #693   +/-   ##  
=======================================  
  Coverage     94.1%   94.1%             
=======================================  
  Files          280     280             
  Lines        29074   29074             
=======================================  
  Hits         27347   27347             
  Misses        1727    1727             
```  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/multiprecision/pull/693?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/multiprecision/pull/693?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [a7094d2...55c7b7c](https://app.codecov.io/gh/boostorg/multiprecision/pull/693?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>  
<details><summary> :rocket: New features to boost your workflow: </summary>  
  
- :snowflake: [Test Analytics](https://docs.codecov.com/docs/test-analytics): Detect flaky tests, report on failures, and find test suite problems.  
</details>

---
