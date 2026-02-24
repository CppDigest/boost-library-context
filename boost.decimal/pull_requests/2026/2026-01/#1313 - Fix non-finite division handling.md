# #1313 Fix non-finite division handling [Merged]

> Username: mborland  
> Created at: 2026-01-22 17:54:56 UTC  
> Updated at: 2026-01-23 01:12:39 UTC  
> Merged at: 2026-01-23 01:12:06 UTC  
> Closed at: 2026-01-23 01:12:06 UTC  
> Url: https://github.com/boostorg/decimal/pull/1313  

Closes: #1312

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2026-01-22 21:45:47 UTC  
> Updated_at: 2026-01-23 01:12:39 UTC  
> Url: https://github.com/boostorg/decimal/pull/1313#issuecomment-3786879840  

## [Codecov](https://app.codecov.io/gh/cppalliance/decimal/pull/1313?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance) Report  
:white_check_mark: All modified and coverable lines are covered by tests.  
:white_check_mark: Project coverage is 98.9%. Comparing base ([`9a4989b`](https://app.codecov.io/gh/cppalliance/decimal/commit/9a4989b234d916c3c0c61d9dadfeb8c25563fef1?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance)) to head ([`fc8fce1`](https://app.codecov.io/gh/cppalliance/decimal/commit/fc8fce141796e93a49da54149914381ba9c189e5?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance)).  
:warning: Report is 5 commits behind head on develop.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/cppalliance/decimal/pull/1313/graphs/tree.svg?width=650&height=150&src=pr&token=drvY8nnV5S&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance)](https://app.codecov.io/gh/cppalliance/decimal/pull/1313?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance)  
  
```diff  
@@            Coverage Diff            @@  
##           develop   #1313     +/-   ##  
=========================================  
+ Coverage     98.9%   98.9%   +0.1%       
=========================================  
  Files          274     275      +1       
  Lines        17869   17886     +17       
  Branches      1908    1908               
=========================================  
+ Hits         17671   17688     +17       
  Misses         198     198               
```  
  
| [Files with missing lines](https://app.codecov.io/gh/cppalliance/decimal/pull/1313?dropdown=coverage&src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance) | Coverage Δ | |  
|---|---|---|  
| [include/boost/decimal/decimal128\_t.hpp](https://app.codecov.io/gh/cppalliance/decimal/pull/1313?src=pr&el=tree&filepath=include%2Fboost%2Fdecimal%2Fdecimal128_t.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance#diff-aW5jbHVkZS9ib29zdC9kZWNpbWFsL2RlY2ltYWwxMjhfdC5ocHA=) | `96.9% <100.0%> (ø)` | |  
| [include/boost/decimal/decimal32\_t.hpp](https://app.codecov.io/gh/cppalliance/decimal/pull/1313?src=pr&el=tree&filepath=include%2Fboost%2Fdecimal%2Fdecimal32_t.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance#diff-aW5jbHVkZS9ib29zdC9kZWNpbWFsL2RlY2ltYWwzMl90LmhwcA==) | `96.5% <100.0%> (ø)` | |  
| [include/boost/decimal/decimal64\_t.hpp](https://app.codecov.io/gh/cppalliance/decimal/pull/1313?src=pr&el=tree&filepath=include%2Fboost%2Fdecimal%2Fdecimal64_t.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance#diff-aW5jbHVkZS9ib29zdC9kZWNpbWFsL2RlY2ltYWw2NF90LmhwcA==) | `95.4% <100.0%> (ø)` | |  
| [include/boost/decimal/decimal\_fast128\_t.hpp](https://app.codecov.io/gh/cppalliance/decimal/pull/1313?src=pr&el=tree&filepath=include%2Fboost%2Fdecimal%2Fdecimal_fast128_t.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance#diff-aW5jbHVkZS9ib29zdC9kZWNpbWFsL2RlY2ltYWxfZmFzdDEyOF90LmhwcA==) | `95.5% <100.0%> (ø)` | |  
| [include/boost/decimal/decimal\_fast32\_t.hpp](https://app.codecov.io/gh/cppalliance/decimal/pull/1313?src=pr&el=tree&filepath=include%2Fboost%2Fdecimal%2Fdecimal_fast32_t.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance#diff-aW5jbHVkZS9ib29zdC9kZWNpbWFsL2RlY2ltYWxfZmFzdDMyX3QuaHBw) | `100.0% <100.0%> (ø)` | |  
| [include/boost/decimal/decimal\_fast64\_t.hpp](https://app.codecov.io/gh/cppalliance/decimal/pull/1313?src=pr&el=tree&filepath=include%2Fboost%2Fdecimal%2Fdecimal_fast64_t.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance#diff-aW5jbHVkZS9ib29zdC9kZWNpbWFsL2RlY2ltYWxfZmFzdDY0X3QuaHBw) | `100.0% <100.0%> (ø)` | |  
| [test/github\_issue\_1312.cpp](https://app.codecov.io/gh/cppalliance/decimal/pull/1313?src=pr&el=tree&filepath=test%2Fgithub_issue_1312.cpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance#diff-dGVzdC9naXRodWJfaXNzdWVfMTMxMi5jcHA=) | `100.0% <100.0%> (ø)` | |  
| [test/random\_decimal64\_math.cpp](https://app.codecov.io/gh/cppalliance/decimal/pull/1313?src=pr&el=tree&filepath=test%2Frandom_decimal64_math.cpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance#diff-dGVzdC9yYW5kb21fZGVjaW1hbDY0X21hdGguY3Bw) | `100.0% <100.0%> (ø)` | |  
  
... and [1 file with indirect coverage changes](https://app.codecov.io/gh/cppalliance/decimal/pull/1313/indirect-changes?src=pr&el=tree-more&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance)  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/cppalliance/decimal/pull/1313?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/cppalliance/decimal/pull/1313?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance). Last update [9a4989b...fc8fce1](https://app.codecov.io/gh/cppalliance/decimal/pull/1313?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance).  
</details>  
<details><summary> :rocket: New features to boost your workflow: </summary>  
  
- :snowflake: [Test Analytics](https://docs.codecov.com/docs/test-analytics): Detect flaky tests, report on failures, and find test suite problems.  
</details>

---
