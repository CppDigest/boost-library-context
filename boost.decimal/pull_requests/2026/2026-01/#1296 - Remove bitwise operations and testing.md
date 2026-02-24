# #1296 Remove bitwise operations and testing [Merged]

> Username: mborland  
> Created at: 2026-01-14 13:38:37 UTC  
> Updated at: 2026-01-14 17:51:20 UTC  
> Merged at: 2026-01-14 17:51:08 UTC  
> Closed at: 2026-01-14 17:51:08 UTC  
> Url: https://github.com/boostorg/decimal/pull/1296  

Closes: #1204

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2026-01-14 17:15:10 UTC  
> Updated_at: 2026-01-14 17:51:20 UTC  
> Url: https://github.com/boostorg/decimal/pull/1296#issuecomment-3750647878  

## [Codecov](https://app.codecov.io/gh/cppalliance/decimal/pull/1296?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance) Report  
:white_check_mark: All modified and coverable lines are covered by tests.  
:white_check_mark: Project coverage is 98.8%. Comparing base ([`1def358`](https://app.codecov.io/gh/cppalliance/decimal/commit/1def3582c28ab23ddcd80160809c80e898306dd5?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance)) to head ([`40e0570`](https://app.codecov.io/gh/cppalliance/decimal/commit/40e0570d19692f1a7ebdb2e5d2902b817025f9c1?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance)).  
:warning: Report is 6 commits behind head on develop.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/cppalliance/decimal/pull/1296/graphs/tree.svg?width=650&height=150&src=pr&token=drvY8nnV5S&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance)](https://app.codecov.io/gh/cppalliance/decimal/pull/1296?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance)  
  
```diff  
@@            Coverage Diff            @@  
##           develop   #1296     +/-   ##  
=========================================  
- Coverage     98.9%   98.8%   -0.0%       
=========================================  
  Files          270     270               
  Lines        17956   17653    -303       
  Branches      1998    1908     -90       
=========================================  
- Hits         17742   17439    -303       
  Misses         214     214               
```  
  
| [Files with missing lines](https://app.codecov.io/gh/cppalliance/decimal/pull/1296?dropdown=coverage&src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance) | Coverage Δ | |  
|---|---|---|  
| [include/boost/decimal/decimal128\_t.hpp](https://app.codecov.io/gh/cppalliance/decimal/pull/1296?src=pr&el=tree&filepath=include%2Fboost%2Fdecimal%2Fdecimal128_t.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance#diff-aW5jbHVkZS9ib29zdC9kZWNpbWFsL2RlY2ltYWwxMjhfdC5ocHA=) | `96.7% <ø> (-0.1%)` | :arrow_down: |  
| [include/boost/decimal/decimal32\_t.hpp](https://app.codecov.io/gh/cppalliance/decimal/pull/1296?src=pr&el=tree&filepath=include%2Fboost%2Fdecimal%2Fdecimal32_t.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance#diff-aW5jbHVkZS9ib29zdC9kZWNpbWFsL2RlY2ltYWwzMl90LmhwcA==) | `96.5% <ø> (-0.1%)` | :arrow_down: |  
| [include/boost/decimal/decimal64\_t.hpp](https://app.codecov.io/gh/cppalliance/decimal/pull/1296?src=pr&el=tree&filepath=include%2Fboost%2Fdecimal%2Fdecimal64_t.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance#diff-aW5jbHVkZS9ib29zdC9kZWNpbWFsL2RlY2ltYWw2NF90LmhwcA==) | `95.1% <ø> (-0.1%)` | :arrow_down: |  
| [include/boost/decimal/detail/cmath/nan.hpp](https://app.codecov.io/gh/cppalliance/decimal/pull/1296?src=pr&el=tree&filepath=include%2Fboost%2Fdecimal%2Fdetail%2Fcmath%2Fnan.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance#diff-aW5jbHVkZS9ib29zdC9kZWNpbWFsL2RldGFpbC9jbWF0aC9uYW4uaHBw) | `92.6% <100.0%> (ø)` | |  
| [include/boost/decimal/detail/write\_payload.hpp](https://app.codecov.io/gh/cppalliance/decimal/pull/1296?src=pr&el=tree&filepath=include%2Fboost%2Fdecimal%2Fdetail%2Fwrite_payload.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance#diff-aW5jbHVkZS9ib29zdC9kZWNpbWFsL2RldGFpbC93cml0ZV9wYXlsb2FkLmhwcA==) | `100.0% <100.0%> (ø)` | |  
| [test/random\_decimal128\_math.cpp](https://app.codecov.io/gh/cppalliance/decimal/pull/1296?src=pr&el=tree&filepath=test%2Frandom_decimal128_math.cpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance#diff-dGVzdC9yYW5kb21fZGVjaW1hbDEyOF9tYXRoLmNwcA==) | `100.0% <ø> (ø)` | |  
| [test/random\_decimal32\_math.cpp](https://app.codecov.io/gh/cppalliance/decimal/pull/1296?src=pr&el=tree&filepath=test%2Frandom_decimal32_math.cpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance#diff-dGVzdC9yYW5kb21fZGVjaW1hbDMyX21hdGguY3Bw) | `100.0% <ø> (ø)` | |  
| [test/random\_decimal64\_math.cpp](https://app.codecov.io/gh/cppalliance/decimal/pull/1296?src=pr&el=tree&filepath=test%2Frandom_decimal64_math.cpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance#diff-dGVzdC9yYW5kb21fZGVjaW1hbDY0X21hdGguY3Bw) | `100.0% <ø> (ø)` | |  
| [test/test\_cmath.cpp](https://app.codecov.io/gh/cppalliance/decimal/pull/1296?src=pr&el=tree&filepath=test%2Ftest_cmath.cpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance#diff-dGVzdC90ZXN0X2NtYXRoLmNwcA==) | `100.0% <100.0%> (ø)` | |  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/cppalliance/decimal/pull/1296?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/cppalliance/decimal/pull/1296?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance). Last update [1def358...40e0570](https://app.codecov.io/gh/cppalliance/decimal/pull/1296?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance).  
</details>  
<details><summary> :rocket: New features to boost your workflow: </summary>  
  
- :snowflake: [Test Analytics](https://docs.codecov.com/docs/test-analytics): Detect flaky tests, report on failures, and find test suite problems.  
</details>

---
