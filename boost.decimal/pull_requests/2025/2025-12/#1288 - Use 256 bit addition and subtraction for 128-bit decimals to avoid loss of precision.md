# #1288 Use 256 bit addition and subtraction for 128-bit decimals to avoid loss of precision  [Merged]

> Username: mborland  
> Created at: 2025-12-17 22:00:19 UTC  
> Updated at: 2026-01-14 00:14:14 UTC  
> Merged at: 2026-01-14 00:13:54 UTC  
> Closed at: 2026-01-14 00:13:54 UTC  
> Url: https://github.com/boostorg/decimal/pull/1288  

Closes: #1287

---

## Comment 1

> Username: ckormanyos  
> Created_at: 2026-01-13 17:26:21 UTC  
> Url: https://github.com/boostorg/decimal/pull/1288#issuecomment-3745537652  

Nice one and nice find, Matt. It's amazing how some of the function tests and awesome client tests really find edge cases

---

## Comment 2

> Username: codecov[bot]  
> Created_at: 2026-01-13 23:55:33 UTC  
> Updated_at: 2026-01-14 00:14:14 UTC  
> Url: https://github.com/boostorg/decimal/pull/1288#issuecomment-3747064651  

## [Codecov](https://app.codecov.io/gh/cppalliance/decimal/pull/1288?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance) Report  
:white_check_mark: All modified and coverable lines are covered by tests.  
:white_check_mark: Project coverage is 98.9%. Comparing base ([`88a0966`](https://app.codecov.io/gh/cppalliance/decimal/commit/88a0966f536be4f96ea3cd4ed7049ec82c9dd93f?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance)) to head ([`653aa3c`](https://app.codecov.io/gh/cppalliance/decimal/commit/653aa3c47037772b768208da28e88e0624682a41?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance)).  
:warning: Report is 48 commits behind head on develop.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/cppalliance/decimal/pull/1288/graphs/tree.svg?width=650&height=150&src=pr&token=drvY8nnV5S&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance)](https://app.codecov.io/gh/cppalliance/decimal/pull/1288?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance)  
  
```diff  
@@            Coverage Diff            @@  
##           develop   #1288     +/-   ##  
=========================================  
- Coverage     99.0%   98.9%   -0.0%       
=========================================  
  Files          269     270      +1       
  Lines        17856   17956    +100       
  Branches      1985    1998     +13       
=========================================  
+ Hits         17661   17742     +81       
- Misses         195     214     +19       
```  
  
| [Files with missing lines](https://app.codecov.io/gh/cppalliance/decimal/pull/1288?dropdown=coverage&src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance) | Coverage Δ | |  
|---|---|---|  
| [include/boost/decimal/decimal128\_t.hpp](https://app.codecov.io/gh/cppalliance/decimal/pull/1288?src=pr&el=tree&filepath=include%2Fboost%2Fdecimal%2Fdecimal128_t.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance#diff-aW5jbHVkZS9ib29zdC9kZWNpbWFsL2RlY2ltYWwxMjhfdC5ocHA=) | `96.8% <100.0%> (-0.1%)` | :arrow_down: |  
| [include/boost/decimal/decimal\_fast128\_t.hpp](https://app.codecov.io/gh/cppalliance/decimal/pull/1288?src=pr&el=tree&filepath=include%2Fboost%2Fdecimal%2Fdecimal_fast128_t.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance#diff-aW5jbHVkZS9ib29zdC9kZWNpbWFsL2RlY2ltYWxfZmFzdDEyOF90LmhwcA==) | `95.2% <100.0%> (-0.2%)` | :arrow_down: |  
| [include/boost/decimal/detail/add\_impl.hpp](https://app.codecov.io/gh/cppalliance/decimal/pull/1288?src=pr&el=tree&filepath=include%2Fboost%2Fdecimal%2Fdetail%2Fadd_impl.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance#diff-aW5jbHVkZS9ib29zdC9kZWNpbWFsL2RldGFpbC9hZGRfaW1wbC5ocHA=) | `85.6% <100.0%> (-12.5%)` | :arrow_down: |  
| [include/boost/decimal/detail/fenv\_rounding.hpp](https://app.codecov.io/gh/cppalliance/decimal/pull/1288?src=pr&el=tree&filepath=include%2Fboost%2Fdecimal%2Fdetail%2Ffenv_rounding.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance#diff-aW5jbHVkZS9ib29zdC9kZWNpbWFsL2RldGFpbC9mZW52X3JvdW5kaW5nLmhwcA==) | `96.7% <100.0%> (+0.4%)` | :arrow_up: |  
| [include/boost/decimal/detail/i256.hpp](https://app.codecov.io/gh/cppalliance/decimal/pull/1288?src=pr&el=tree&filepath=include%2Fboost%2Fdecimal%2Fdetail%2Fi256.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance#diff-aW5jbHVkZS9ib29zdC9kZWNpbWFsL2RldGFpbC9pMjU2LmhwcA==) | `100.0% <100.0%> (ø)` | |  
| [include/boost/decimal/detail/normalize.hpp](https://app.codecov.io/gh/cppalliance/decimal/pull/1288?src=pr&el=tree&filepath=include%2Fboost%2Fdecimal%2Fdetail%2Fnormalize.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance#diff-aW5jbHVkZS9ib29zdC9kZWNpbWFsL2RldGFpbC9ub3JtYWxpemUuaHBw) | `100.0% <100.0%> (ø)` | |  
| [include/boost/decimal/detail/power\_tables.hpp](https://app.codecov.io/gh/cppalliance/decimal/pull/1288?src=pr&el=tree&filepath=include%2Fboost%2Fdecimal%2Fdetail%2Fpower_tables.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance#diff-aW5jbHVkZS9ib29zdC9kZWNpbWFsL2RldGFpbC9wb3dlcl90YWJsZXMuaHBw) | `100.0% <100.0%> (ø)` | |  
| [include/boost/decimal/detail/u256.hpp](https://app.codecov.io/gh/cppalliance/decimal/pull/1288?src=pr&el=tree&filepath=include%2Fboost%2Fdecimal%2Fdetail%2Fu256.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance#diff-aW5jbHVkZS9ib29zdC9kZWNpbWFsL2RldGFpbC91MjU2LmhwcA==) | `100.0% <100.0%> (+0.8%)` | :arrow_up: |  
| [test/github\_issue\_1260.cpp](https://app.codecov.io/gh/cppalliance/decimal/pull/1288?src=pr&el=tree&filepath=test%2Fgithub_issue_1260.cpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance#diff-dGVzdC9naXRodWJfaXNzdWVfMTI2MC5jcHA=) | `100.0% <100.0%> (ø)` | |  
| [test/test\_big\_uints.cpp](https://app.codecov.io/gh/cppalliance/decimal/pull/1288?src=pr&el=tree&filepath=test%2Ftest_big_uints.cpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance#diff-dGVzdC90ZXN0X2JpZ191aW50cy5jcHA=) | `100.0% <100.0%> (ø)` | |  
  
... and [1 file with indirect coverage changes](https://app.codecov.io/gh/cppalliance/decimal/pull/1288/indirect-changes?src=pr&el=tree-more&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance)  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/cppalliance/decimal/pull/1288?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/cppalliance/decimal/pull/1288?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance). Last update [88a0966...653aa3c](https://app.codecov.io/gh/cppalliance/decimal/pull/1288?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance).  
</details>  
<details><summary> :rocket: New features to boost your workflow: </summary>  
  
- :snowflake: [Test Analytics](https://docs.codecov.com/docs/test-analytics): Detect flaky tests, report on failures, and find test suite problems.  
</details>

---
