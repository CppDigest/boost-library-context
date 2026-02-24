# #1293 Fix various warnings [Merged]

> Username: mborland  
> Created at: 2026-01-09 17:27:47 UTC  
> Updated at: 2026-01-10 01:05:01 UTC  
> Merged at: 2026-01-10 00:43:14 UTC  
> Closed at: 2026-01-10 00:43:14 UTC  
> Url: https://github.com/boostorg/decimal/pull/1293  

Closes: #1291

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2026-01-10 00:54:28 UTC  
> Updated_at: 2026-01-10 01:05:01 UTC  
> Url: https://github.com/boostorg/decimal/pull/1293#issuecomment-3731170883  

## [Codecov](https://app.codecov.io/gh/cppalliance/decimal/pull/1293?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance) Report  
:x: Patch coverage is `97.10145%` with `2 lines` in your changes missing coverage. Please review.  
:white_check_mark: Project coverage is 98.9%. Comparing base ([`9489bbd`](https://app.codecov.io/gh/cppalliance/decimal/commit/9489bbdb774b5b1a797001836be1653ad3b22b1e?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance)) to head ([`5d73132`](https://app.codecov.io/gh/cppalliance/decimal/commit/5d731326da81ea7a0e91f03cc9f363e203f0252a?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance)).  
:warning: Report is 9 commits behind head on develop.  
  
| [Files with missing lines](https://app.codecov.io/gh/cppalliance/decimal/pull/1293?dropdown=coverage&src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance) | Patch % | Lines |  
|---|---|---|  
| [include/boost/decimal/decimal128\_t.hpp](https://app.codecov.io/gh/cppalliance/decimal/pull/1293?src=pr&el=tree&filepath=include%2Fboost%2Fdecimal%2Fdecimal128_t.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance#diff-aW5jbHVkZS9ib29zdC9kZWNpbWFsL2RlY2ltYWwxMjhfdC5ocHA=) | 0.0% | [1 Missing :warning: ](https://app.codecov.io/gh/cppalliance/decimal/pull/1293?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance) |  
| [include/boost/decimal/decimal\_fast128\_t.hpp](https://app.codecov.io/gh/cppalliance/decimal/pull/1293?src=pr&el=tree&filepath=include%2Fboost%2Fdecimal%2Fdecimal_fast128_t.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance#diff-aW5jbHVkZS9ib29zdC9kZWNpbWFsL2RlY2ltYWxfZmFzdDEyOF90LmhwcA==) | 0.0% | [1 Missing :warning: ](https://app.codecov.io/gh/cppalliance/decimal/pull/1293?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance) |  
  
<details><summary>Additional details and impacted files</summary>  
  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/cppalliance/decimal/pull/1293/graphs/tree.svg?width=650&height=150&src=pr&token=drvY8nnV5S&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance)](https://app.codecov.io/gh/cppalliance/decimal/pull/1293?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance)  
  
```diff  
@@            Coverage Diff            @@  
##           develop   #1293     +/-   ##  
=========================================  
- Coverage     99.0%   98.9%   -0.0%       
=========================================  
  Files          268     268               
  Lines        17754   17823     +69       
  Branches      1984    1985      +1       
=========================================  
+ Hits         17561   17624     +63       
- Misses         193     199      +6       
```  
  
| [Files with missing lines](https://app.codecov.io/gh/cppalliance/decimal/pull/1293?dropdown=coverage&src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance) | Coverage Δ | |  
|---|---|---|  
| [include/boost/decimal/cstdio.hpp](https://app.codecov.io/gh/cppalliance/decimal/pull/1293?src=pr&el=tree&filepath=include%2Fboost%2Fdecimal%2Fcstdio.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance#diff-aW5jbHVkZS9ib29zdC9kZWNpbWFsL2NzdGRpby5ocHA=) | `97.6% <ø> (ø)` | |  
| [include/boost/decimal/decimal32\_t.hpp](https://app.codecov.io/gh/cppalliance/decimal/pull/1293?src=pr&el=tree&filepath=include%2Fboost%2Fdecimal%2Fdecimal32_t.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance#diff-aW5jbHVkZS9ib29zdC9kZWNpbWFsL2RlY2ltYWwzMl90LmhwcA==) | `96.7% <100.0%> (ø)` | |  
| [include/boost/decimal/decimal64\_t.hpp](https://app.codecov.io/gh/cppalliance/decimal/pull/1293?src=pr&el=tree&filepath=include%2Fboost%2Fdecimal%2Fdecimal64_t.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance#diff-aW5jbHVkZS9ib29zdC9kZWNpbWFsL2RlY2ltYWw2NF90LmhwcA==) | `95.3% <100.0%> (ø)` | |  
| [include/boost/decimal/decimal\_fast32\_t.hpp](https://app.codecov.io/gh/cppalliance/decimal/pull/1293?src=pr&el=tree&filepath=include%2Fboost%2Fdecimal%2Fdecimal_fast32_t.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance#diff-aW5jbHVkZS9ib29zdC9kZWNpbWFsL2RlY2ltYWxfZmFzdDMyX3QuaHBw) | `100.0% <100.0%> (ø)` | |  
| [include/boost/decimal/decimal\_fast64\_t.hpp](https://app.codecov.io/gh/cppalliance/decimal/pull/1293?src=pr&el=tree&filepath=include%2Fboost%2Fdecimal%2Fdecimal_fast64_t.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance#diff-aW5jbHVkZS9ib29zdC9kZWNpbWFsL2RlY2ltYWxfZmFzdDY0X3QuaHBw) | `100.0% <100.0%> (ø)` | |  
| [include/boost/decimal/detail/cmath/atan.hpp](https://app.codecov.io/gh/cppalliance/decimal/pull/1293?src=pr&el=tree&filepath=include%2Fboost%2Fdecimal%2Fdetail%2Fcmath%2Fatan.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance#diff-aW5jbHVkZS9ib29zdC9kZWNpbWFsL2RldGFpbC9jbWF0aC9hdGFuLmhwcA==) | `100.0% <100.0%> (+3.6%)` | :arrow_up: |  
| [include/boost/decimal/detail/cmath/cos.hpp](https://app.codecov.io/gh/cppalliance/decimal/pull/1293?src=pr&el=tree&filepath=include%2Fboost%2Fdecimal%2Fdetail%2Fcmath%2Fcos.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance#diff-aW5jbHVkZS9ib29zdC9kZWNpbWFsL2RldGFpbC9jbWF0aC9jb3MuaHBw) | `100.0% <100.0%> (ø)` | |  
| [include/boost/decimal/detail/cmath/exp.hpp](https://app.codecov.io/gh/cppalliance/decimal/pull/1293?src=pr&el=tree&filepath=include%2Fboost%2Fdecimal%2Fdetail%2Fcmath%2Fexp.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance#diff-aW5jbHVkZS9ib29zdC9kZWNpbWFsL2RldGFpbC9jbWF0aC9leHAuaHBw) | `100.0% <100.0%> (ø)` | |  
| [include/boost/decimal/detail/cmath/frexp.hpp](https://app.codecov.io/gh/cppalliance/decimal/pull/1293?src=pr&el=tree&filepath=include%2Fboost%2Fdecimal%2Fdetail%2Fcmath%2Ffrexp.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance#diff-aW5jbHVkZS9ib29zdC9kZWNpbWFsL2RldGFpbC9jbWF0aC9mcmV4cC5ocHA=) | `92.0% <100.0%> (-0.3%)` | :arrow_down: |  
| [include/boost/decimal/detail/cmath/ilogb.hpp](https://app.codecov.io/gh/cppalliance/decimal/pull/1293?src=pr&el=tree&filepath=include%2Fboost%2Fdecimal%2Fdetail%2Fcmath%2Filogb.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance#diff-aW5jbHVkZS9ib29zdC9kZWNpbWFsL2RldGFpbC9jbWF0aC9pbG9nYi5ocHA=) | `100.0% <ø> (ø)` | |  
| ... and [80 more](https://app.codecov.io/gh/cppalliance/decimal/pull/1293?src=pr&el=tree-more&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance) | |  
  
... and [22 files with indirect coverage changes](https://app.codecov.io/gh/cppalliance/decimal/pull/1293/indirect-changes?src=pr&el=tree-more&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance)  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/cppalliance/decimal/pull/1293?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/cppalliance/decimal/pull/1293?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance). Last update [9489bbd...5d73132](https://app.codecov.io/gh/cppalliance/decimal/pull/1293?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance).  
</details>  
<details><summary> :rocket: New features to boost your workflow: </summary>  
  
- :snowflake: [Test Analytics](https://docs.codecov.com/docs/test-analytics): Detect flaky tests, report on failures, and find test suite problems.  
</details>

---
