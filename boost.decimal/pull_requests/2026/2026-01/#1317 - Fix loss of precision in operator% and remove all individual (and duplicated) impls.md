# #1317 Fix loss of precision in operator% and remove all individual (and duplicated) impls [Merged]

> Username: mborland  
> Created at: 2026-01-23 16:41:43 UTC  
> Updated at: 2026-01-23 20:10:44 UTC  
> Merged at: 2026-01-23 19:47:45 UTC  
> Closed at: 2026-01-23 19:47:45 UTC  
> Url: https://github.com/boostorg/decimal/pull/1317  

Closes: #1314

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2026-01-23 19:59:33 UTC  
> Updated_at: 2026-01-23 20:10:44 UTC  
> Url: https://github.com/boostorg/decimal/pull/1317#issuecomment-3792131374  

## [Codecov](https://app.codecov.io/gh/cppalliance/decimal/pull/1317?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance) Report  
:x: Patch coverage is `88.88889%` with `4 lines` in your changes missing coverage. Please review.  
:white_check_mark: Project coverage is 98.9%. Comparing base ([`a8abd00`](https://app.codecov.io/gh/cppalliance/decimal/commit/a8abd0067657ef1f14f1ba98ff982b8b58a3223c?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance)) to head ([`d3ecf65`](https://app.codecov.io/gh/cppalliance/decimal/commit/d3ecf65f1d6ee0c0c72fadc4d132ed279db11d73?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance)).  
:warning: Report is 17 commits behind head on develop.  
  
| [Files with missing lines](https://app.codecov.io/gh/cppalliance/decimal/pull/1317?dropdown=coverage&src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance) | Patch % | Lines |  
|---|---|---|  
| [include/boost/decimal/detail/mod\_impl.hpp](https://app.codecov.io/gh/cppalliance/decimal/pull/1317?src=pr&el=tree&filepath=include%2Fboost%2Fdecimal%2Fdetail%2Fmod_impl.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance#diff-aW5jbHVkZS9ib29zdC9kZWNpbWFsL2RldGFpbC9tb2RfaW1wbC5ocHA=) | 82.4% | [3 Missing :warning: ](https://app.codecov.io/gh/cppalliance/decimal/pull/1317?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance) |  
| [include/boost/decimal/decimal\_fast128\_t.hpp](https://app.codecov.io/gh/cppalliance/decimal/pull/1317?src=pr&el=tree&filepath=include%2Fboost%2Fdecimal%2Fdecimal_fast128_t.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance#diff-aW5jbHVkZS9ib29zdC9kZWNpbWFsL2RlY2ltYWxfZmFzdDEyOF90LmhwcA==) | 0.0% | [1 Missing :warning: ](https://app.codecov.io/gh/cppalliance/decimal/pull/1317?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance) |  
  
<details><summary>Additional details and impacted files</summary>  
  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/cppalliance/decimal/pull/1317/graphs/tree.svg?width=650&height=150&src=pr&token=drvY8nnV5S&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance)](https://app.codecov.io/gh/cppalliance/decimal/pull/1317?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance)  
  
```diff  
@@            Coverage Diff            @@  
##           develop   #1317     +/-   ##  
=========================================  
+ Coverage     98.9%   98.9%   +0.1%       
=========================================  
  Files          275     276      +1       
  Lines        17886   17902     +16       
  Branches      1908    1909      +1       
=========================================  
+ Hits         17688   17704     +16       
  Misses         198     198               
```  
  
| [Files with missing lines](https://app.codecov.io/gh/cppalliance/decimal/pull/1317?dropdown=coverage&src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance) | Coverage Δ | |  
|---|---|---|  
| [include/boost/decimal/decimal128\_t.hpp](https://app.codecov.io/gh/cppalliance/decimal/pull/1317?src=pr&el=tree&filepath=include%2Fboost%2Fdecimal%2Fdecimal128_t.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance#diff-aW5jbHVkZS9ib29zdC9kZWNpbWFsL2RlY2ltYWwxMjhfdC5ocHA=) | `98.2% <100.0%> (+1.4%)` | :arrow_up: |  
| [include/boost/decimal/decimal32\_t.hpp](https://app.codecov.io/gh/cppalliance/decimal/pull/1317?src=pr&el=tree&filepath=include%2Fboost%2Fdecimal%2Fdecimal32_t.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance#diff-aW5jbHVkZS9ib29zdC9kZWNpbWFsL2RlY2ltYWwzMl90LmhwcA==) | `96.5% <100.0%> (-<0.1%)` | :arrow_down: |  
| [include/boost/decimal/decimal64\_t.hpp](https://app.codecov.io/gh/cppalliance/decimal/pull/1317?src=pr&el=tree&filepath=include%2Fboost%2Fdecimal%2Fdecimal64_t.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance#diff-aW5jbHVkZS9ib29zdC9kZWNpbWFsL2RlY2ltYWw2NF90LmhwcA==) | `95.3% <100.0%> (-<0.1%)` | :arrow_down: |  
| [include/boost/decimal/decimal\_fast32\_t.hpp](https://app.codecov.io/gh/cppalliance/decimal/pull/1317?src=pr&el=tree&filepath=include%2Fboost%2Fdecimal%2Fdecimal_fast32_t.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance#diff-aW5jbHVkZS9ib29zdC9kZWNpbWFsL2RlY2ltYWxfZmFzdDMyX3QuaHBw) | `100.0% <100.0%> (ø)` | |  
| [include/boost/decimal/decimal\_fast64\_t.hpp](https://app.codecov.io/gh/cppalliance/decimal/pull/1317?src=pr&el=tree&filepath=include%2Fboost%2Fdecimal%2Fdecimal_fast64_t.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance#diff-aW5jbHVkZS9ib29zdC9kZWNpbWFsL2RlY2ltYWxfZmFzdDY0X3QuaHBw) | `100.0% <100.0%> (ø)` | |  
| [include/boost/decimal/detail/power\_tables.hpp](https://app.codecov.io/gh/cppalliance/decimal/pull/1317?src=pr&el=tree&filepath=include%2Fboost%2Fdecimal%2Fdetail%2Fpower_tables.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance#diff-aW5jbHVkZS9ib29zdC9kZWNpbWFsL2RldGFpbC9wb3dlcl90YWJsZXMuaHBw) | `100.0% <100.0%> (ø)` | |  
| [include/boost/decimal/detail/u256.hpp](https://app.codecov.io/gh/cppalliance/decimal/pull/1317?src=pr&el=tree&filepath=include%2Fboost%2Fdecimal%2Fdetail%2Fu256.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance#diff-aW5jbHVkZS9ib29zdC9kZWNpbWFsL2RldGFpbC91MjU2LmhwcA==) | `96.4% <ø> (-3.6%)` | :arrow_down: |  
| [test/random\_decimal128\_math.cpp](https://app.codecov.io/gh/cppalliance/decimal/pull/1317?src=pr&el=tree&filepath=test%2Frandom_decimal128_math.cpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance#diff-dGVzdC9yYW5kb21fZGVjaW1hbDEyOF9tYXRoLmNwcA==) | `100.0% <100.0%> (ø)` | |  
| [test/random\_decimal32\_math.cpp](https://app.codecov.io/gh/cppalliance/decimal/pull/1317?src=pr&el=tree&filepath=test%2Frandom_decimal32_math.cpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance#diff-dGVzdC9yYW5kb21fZGVjaW1hbDMyX21hdGguY3Bw) | `100.0% <100.0%> (ø)` | |  
| [test/random\_decimal64\_math.cpp](https://app.codecov.io/gh/cppalliance/decimal/pull/1317?src=pr&el=tree&filepath=test%2Frandom_decimal64_math.cpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance#diff-dGVzdC9yYW5kb21fZGVjaW1hbDY0X21hdGguY3Bw) | `100.0% <100.0%> (ø)` | |  
| ... and [2 more](https://app.codecov.io/gh/cppalliance/decimal/pull/1317?src=pr&el=tree-more&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance) | |  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/cppalliance/decimal/pull/1317?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/cppalliance/decimal/pull/1317?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance). Last update [a8abd00...d3ecf65](https://app.codecov.io/gh/cppalliance/decimal/pull/1317?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance).  
</details>  
<details><summary> :rocket: New features to boost your workflow: </summary>  
  
- :snowflake: [Test Analytics](https://docs.codecov.com/docs/test-analytics): Detect flaky tests, report on failures, and find test suite problems.  
</details>

---
