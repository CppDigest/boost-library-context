# #1305 Fix case of finite x - inf not returning -inf [Merged]

> Username: mborland  
> Created at: 2026-01-20 15:38:05 UTC  
> Updated at: 2026-01-20 18:56:18 UTC  
> Merged at: 2026-01-20 18:56:10 UTC  
> Closed at: 2026-01-20 18:56:10 UTC  
> Url: https://github.com/boostorg/decimal/pull/1305  

Closes: #1304

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2026-01-20 18:14:40 UTC  
> Updated_at: 2026-01-20 18:56:18 UTC  
> Url: https://github.com/boostorg/decimal/pull/1305#issuecomment-3774291563  

## [Codecov](https://app.codecov.io/gh/cppalliance/decimal/pull/1305?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance) Report  
:x: Patch coverage is `97.77778%` with `1 line` in your changes missing coverage. Please review.  
:white_check_mark: Project coverage is 98.9%. Comparing base ([`03b63a5`](https://app.codecov.io/gh/cppalliance/decimal/commit/03b63a53a3fd146f4a7683b44e3156386b15b803?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance)) to head ([`8732359`](https://app.codecov.io/gh/cppalliance/decimal/commit/87323592a9c5256cdd4cbac64c81b2df4caa4c7e?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance)).  
:warning: Report is 5 commits behind head on develop.  
  
| [Files with missing lines](https://app.codecov.io/gh/cppalliance/decimal/pull/1305?dropdown=coverage&src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance) | Patch % | Lines |  
|---|---|---|  
| [include/boost/decimal/decimal32\_t.hpp](https://app.codecov.io/gh/cppalliance/decimal/pull/1305?src=pr&el=tree&filepath=include%2Fboost%2Fdecimal%2Fdecimal32_t.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance#diff-aW5jbHVkZS9ib29zdC9kZWNpbWFsL2RlY2ltYWwzMl90LmhwcA==) | 75.0% | [1 Missing :warning: ](https://app.codecov.io/gh/cppalliance/decimal/pull/1305?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance) |  
  
<details><summary>Additional details and impacted files</summary>  
  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/cppalliance/decimal/pull/1305/graphs/tree.svg?width=650&height=150&src=pr&token=drvY8nnV5S&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance)](https://app.codecov.io/gh/cppalliance/decimal/pull/1305?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance)  
  
```diff  
@@            Coverage Diff            @@  
##           develop   #1305     +/-   ##  
=========================================  
- Coverage     98.9%   98.9%   -0.0%       
=========================================  
  Files          272     273      +1       
  Lines        17792   17841     +49       
  Branches      1893    1904     +11       
=========================================  
+ Hits         17596   17644     +48       
- Misses         196     197      +1       
```  
  
| [Files with missing lines](https://app.codecov.io/gh/cppalliance/decimal/pull/1305?dropdown=coverage&src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance) | Coverage Δ | |  
|---|---|---|  
| [include/boost/decimal/decimal128\_t.hpp](https://app.codecov.io/gh/cppalliance/decimal/pull/1305?src=pr&el=tree&filepath=include%2Fboost%2Fdecimal%2Fdecimal128_t.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance#diff-aW5jbHVkZS9ib29zdC9kZWNpbWFsL2RlY2ltYWwxMjhfdC5ocHA=) | `96.8% <100.0%> (+0.1%)` | :arrow_up: |  
| [include/boost/decimal/decimal64\_t.hpp](https://app.codecov.io/gh/cppalliance/decimal/pull/1305?src=pr&el=tree&filepath=include%2Fboost%2Fdecimal%2Fdecimal64_t.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance#diff-aW5jbHVkZS9ib29zdC9kZWNpbWFsL2RlY2ltYWw2NF90LmhwcA==) | `95.3% <100.0%> (+0.1%)` | :arrow_up: |  
| [include/boost/decimal/decimal\_fast128\_t.hpp](https://app.codecov.io/gh/cppalliance/decimal/pull/1305?src=pr&el=tree&filepath=include%2Fboost%2Fdecimal%2Fdecimal_fast128_t.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance#diff-aW5jbHVkZS9ib29zdC9kZWNpbWFsL2RlY2ltYWxfZmFzdDEyOF90LmhwcA==) | `95.5% <100.0%> (+0.1%)` | :arrow_up: |  
| [include/boost/decimal/decimal\_fast32\_t.hpp](https://app.codecov.io/gh/cppalliance/decimal/pull/1305?src=pr&el=tree&filepath=include%2Fboost%2Fdecimal%2Fdecimal_fast32_t.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance#diff-aW5jbHVkZS9ib29zdC9kZWNpbWFsL2RlY2ltYWxfZmFzdDMyX3QuaHBw) | `100.0% <100.0%> (ø)` | |  
| [include/boost/decimal/decimal\_fast64\_t.hpp](https://app.codecov.io/gh/cppalliance/decimal/pull/1305?src=pr&el=tree&filepath=include%2Fboost%2Fdecimal%2Fdecimal_fast64_t.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance#diff-aW5jbHVkZS9ib29zdC9kZWNpbWFsL2RlY2ltYWxfZmFzdDY0X3QuaHBw) | `100.0% <100.0%> (ø)` | |  
| [test/github\_issue\_1304.cpp](https://app.codecov.io/gh/cppalliance/decimal/pull/1305?src=pr&el=tree&filepath=test%2Fgithub_issue_1304.cpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance#diff-dGVzdC9naXRodWJfaXNzdWVfMTMwNC5jcHA=) | `100.0% <100.0%> (ø)` | |  
| [include/boost/decimal/decimal32\_t.hpp](https://app.codecov.io/gh/cppalliance/decimal/pull/1305?src=pr&el=tree&filepath=include%2Fboost%2Fdecimal%2Fdecimal32_t.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance#diff-aW5jbHVkZS9ib29zdC9kZWNpbWFsL2RlY2ltYWwzMl90LmhwcA==) | `96.4% <75.0%> (-0.2%)` | :arrow_down: |  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/cppalliance/decimal/pull/1305?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/cppalliance/decimal/pull/1305?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance). Last update [03b63a5...8732359](https://app.codecov.io/gh/cppalliance/decimal/pull/1305?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance).  
</details>  
<details><summary> :rocket: New features to boost your workflow: </summary>  
  
- :snowflake: [Test Analytics](https://docs.codecov.com/docs/test-analytics): Detect flaky tests, report on failures, and find test suite problems.  
</details>

---
