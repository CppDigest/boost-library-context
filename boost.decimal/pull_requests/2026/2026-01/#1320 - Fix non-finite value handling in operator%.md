# #1320 Fix non-finite value handling in operator% [Merged]

> Username: mborland  
> Created at: 2026-01-23 21:08:51 UTC  
> Updated at: 2026-01-26 18:15:05 UTC  
> Merged at: 2026-01-26 18:14:46 UTC  
> Closed at: 2026-01-26 18:14:46 UTC  
> Url: https://github.com/boostorg/decimal/pull/1320  

Closes: #1319

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2026-01-26 18:11:34 UTC  
> Updated_at: 2026-01-26 18:15:05 UTC  
> Url: https://github.com/boostorg/decimal/pull/1320#issuecomment-3800984364  

## [Codecov](https://app.codecov.io/gh/cppalliance/decimal/pull/1320?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance) Report  
:x: Patch coverage is `78.44828%` with `25 lines` in your changes missing coverage. Please review.  
:white_check_mark: Project coverage is 98.8%. Comparing base ([`bd99e1d`](https://app.codecov.io/gh/cppalliance/decimal/commit/bd99e1da7b77b7b26f2cb11a1bf2142ab2caba85?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance)) to head ([`41a22b6`](https://app.codecov.io/gh/cppalliance/decimal/commit/41a22b68435900b3a088b6e6cba682eb45915234?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance)).  
:warning: Report is 10 commits behind head on develop.  
  
| [Files with missing lines](https://app.codecov.io/gh/cppalliance/decimal/pull/1320?dropdown=coverage&src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance) | Patch % | Lines |  
|---|---|---|  
| [include/boost/decimal/decimal\_fast128\_t.hpp](https://app.codecov.io/gh/cppalliance/decimal/pull/1320?src=pr&el=tree&filepath=include%2Fboost%2Fdecimal%2Fdecimal_fast128_t.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance#diff-aW5jbHVkZS9ib29zdC9kZWNpbWFsL2RlY2ltYWxfZmFzdDEyOF90LmhwcA==) | 62.5% | [6 Missing :warning: ](https://app.codecov.io/gh/cppalliance/decimal/pull/1320?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance) |  
| [include/boost/decimal/decimal128\_t.hpp](https://app.codecov.io/gh/cppalliance/decimal/pull/1320?src=pr&el=tree&filepath=include%2Fboost%2Fdecimal%2Fdecimal128_t.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance#diff-aW5jbHVkZS9ib29zdC9kZWNpbWFsL2RlY2ltYWwxMjhfdC5ocHA=) | 75.0% | [4 Missing :warning: ](https://app.codecov.io/gh/cppalliance/decimal/pull/1320?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance) |  
| [include/boost/decimal/decimal32\_t.hpp](https://app.codecov.io/gh/cppalliance/decimal/pull/1320?src=pr&el=tree&filepath=include%2Fboost%2Fdecimal%2Fdecimal32_t.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance#diff-aW5jbHVkZS9ib29zdC9kZWNpbWFsL2RlY2ltYWwzMl90LmhwcA==) | 75.0% | [4 Missing :warning: ](https://app.codecov.io/gh/cppalliance/decimal/pull/1320?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance) |  
| [include/boost/decimal/decimal\_fast32\_t.hpp](https://app.codecov.io/gh/cppalliance/decimal/pull/1320?src=pr&el=tree&filepath=include%2Fboost%2Fdecimal%2Fdecimal_fast32_t.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance#diff-aW5jbHVkZS9ib29zdC9kZWNpbWFsL2RlY2ltYWxfZmFzdDMyX3QuaHBw) | 75.0% | [4 Missing :warning: ](https://app.codecov.io/gh/cppalliance/decimal/pull/1320?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance) |  
| [include/boost/decimal/decimal\_fast64\_t.hpp](https://app.codecov.io/gh/cppalliance/decimal/pull/1320?src=pr&el=tree&filepath=include%2Fboost%2Fdecimal%2Fdecimal_fast64_t.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance#diff-aW5jbHVkZS9ib29zdC9kZWNpbWFsL2RlY2ltYWxfZmFzdDY0X3QuaHBw) | 75.0% | [4 Missing :warning: ](https://app.codecov.io/gh/cppalliance/decimal/pull/1320?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance) |  
| [include/boost/decimal/decimal64\_t.hpp](https://app.codecov.io/gh/cppalliance/decimal/pull/1320?src=pr&el=tree&filepath=include%2Fboost%2Fdecimal%2Fdecimal64_t.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance#diff-aW5jbHVkZS9ib29zdC9kZWNpbWFsL2RlY2ltYWw2NF90LmhwcA==) | 81.3% | [3 Missing :warning: ](https://app.codecov.io/gh/cppalliance/decimal/pull/1320?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance) |  
  
<details><summary>Additional details and impacted files</summary>  
  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/cppalliance/decimal/pull/1320/graphs/tree.svg?width=650&height=150&src=pr&token=drvY8nnV5S&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance)](https://app.codecov.io/gh/cppalliance/decimal/pull/1320?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance)  
  
```diff  
@@            Coverage Diff            @@  
##           develop   #1320     +/-   ##  
=========================================  
- Coverage     99.0%   98.8%   -0.1%       
=========================================  
  Files          276     277      +1       
  Lines        17902   18008    +106       
  Branches      1909    1915      +6       
=========================================  
+ Hits         17706   17785     +79       
- Misses         196     223     +27       
```  
  
| [Files with missing lines](https://app.codecov.io/gh/cppalliance/decimal/pull/1320?dropdown=coverage&src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance) | Coverage Δ | |  
|---|---|---|  
| [test/github\_issue\_1319.cpp](https://app.codecov.io/gh/cppalliance/decimal/pull/1320?src=pr&el=tree&filepath=test%2Fgithub_issue_1319.cpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance#diff-dGVzdC9naXRodWJfaXNzdWVfMTMxOS5jcHA=) | `100.0% <100.0%> (ø)` | |  
| [test/random\_decimal64\_math.cpp](https://app.codecov.io/gh/cppalliance/decimal/pull/1320?src=pr&el=tree&filepath=test%2Frandom_decimal64_math.cpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance#diff-dGVzdC9yYW5kb21fZGVjaW1hbDY0X21hdGguY3Bw) | `100.0% <100.0%> (ø)` | |  
| [include/boost/decimal/decimal64\_t.hpp](https://app.codecov.io/gh/cppalliance/decimal/pull/1320?src=pr&el=tree&filepath=include%2Fboost%2Fdecimal%2Fdecimal64_t.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance#diff-aW5jbHVkZS9ib29zdC9kZWNpbWFsL2RlY2ltYWw2NF90LmhwcA==) | `94.8% <81.3%> (-0.5%)` | :arrow_down: |  
| [include/boost/decimal/decimal128\_t.hpp](https://app.codecov.io/gh/cppalliance/decimal/pull/1320?src=pr&el=tree&filepath=include%2Fboost%2Fdecimal%2Fdecimal128_t.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance#diff-aW5jbHVkZS9ib29zdC9kZWNpbWFsL2RlY2ltYWwxMjhfdC5ocHA=) | `97.2% <75.0%> (-0.9%)` | :arrow_down: |  
| [include/boost/decimal/decimal32\_t.hpp](https://app.codecov.io/gh/cppalliance/decimal/pull/1320?src=pr&el=tree&filepath=include%2Fboost%2Fdecimal%2Fdecimal32_t.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance#diff-aW5jbHVkZS9ib29zdC9kZWNpbWFsL2RlY2ltYWwzMl90LmhwcA==) | `95.7% <75.0%> (-0.8%)` | :arrow_down: |  
| [include/boost/decimal/decimal\_fast32\_t.hpp](https://app.codecov.io/gh/cppalliance/decimal/pull/1320?src=pr&el=tree&filepath=include%2Fboost%2Fdecimal%2Fdecimal_fast32_t.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance#diff-aW5jbHVkZS9ib29zdC9kZWNpbWFsL2RlY2ltYWxfZmFzdDMyX3QuaHBw) | `98.8% <75.0%> (-1.2%)` | :arrow_down: |  
| [include/boost/decimal/decimal\_fast64\_t.hpp](https://app.codecov.io/gh/cppalliance/decimal/pull/1320?src=pr&el=tree&filepath=include%2Fboost%2Fdecimal%2Fdecimal_fast64_t.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance#diff-aW5jbHVkZS9ib29zdC9kZWNpbWFsL2RlY2ltYWxfZmFzdDY0X3QuaHBw) | `98.7% <75.0%> (-1.3%)` | :arrow_down: |  
| [include/boost/decimal/decimal\_fast128\_t.hpp](https://app.codecov.io/gh/cppalliance/decimal/pull/1320?src=pr&el=tree&filepath=include%2Fboost%2Fdecimal%2Fdecimal_fast128_t.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance#diff-aW5jbHVkZS9ib29zdC9kZWNpbWFsL2RlY2ltYWxfZmFzdDEyOF90LmhwcA==) | `95.3% <62.5%> (-1.5%)` | :arrow_down: |  
  
... and [2 files with indirect coverage changes](https://app.codecov.io/gh/cppalliance/decimal/pull/1320/indirect-changes?src=pr&el=tree-more&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance)  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/cppalliance/decimal/pull/1320?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/cppalliance/decimal/pull/1320?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance). Last update [bd99e1d...41a22b6](https://app.codecov.io/gh/cppalliance/decimal/pull/1320?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance).  
</details>  
<details><summary> :rocket: New features to boost your workflow: </summary>  
  
- :snowflake: [Test Analytics](https://docs.codecov.com/docs/test-analytics): Detect flaky tests, report on failures, and find test suite problems.  
</details>

---
