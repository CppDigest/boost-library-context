# #1297 Remove now unused d128 addition impl [Merged]

> Username: mborland  
> Created at: 2026-01-14 14:28:50 UTC  
> Updated at: 2026-01-14 18:58:26 UTC  
> Merged at: 2026-01-14 18:58:10 UTC  
> Closed at: 2026-01-14 18:58:10 UTC  
> Url: https://github.com/boostorg/decimal/pull/1297  



---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2026-01-14 17:37:58 UTC  
> Updated_at: 2026-01-14 18:58:26 UTC  
> Url: https://github.com/boostorg/decimal/pull/1297#issuecomment-3750779359  

## [Codecov](https://app.codecov.io/gh/cppalliance/decimal/pull/1297?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance) Report  
:white_check_mark: All modified and coverable lines are covered by tests.  
:white_check_mark: Project coverage is 99.0%. Comparing base ([`1def358`](https://app.codecov.io/gh/cppalliance/decimal/commit/1def3582c28ab23ddcd80160809c80e898306dd5?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance)) to head ([`21f218e`](https://app.codecov.io/gh/cppalliance/decimal/commit/21f218e640cdbc8dba2f172f27fb5bf1bec7fe96?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance)).  
:warning: Report is 10 commits behind head on develop.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/cppalliance/decimal/pull/1297/graphs/tree.svg?width=650&height=150&src=pr&token=drvY8nnV5S&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance)](https://app.codecov.io/gh/cppalliance/decimal/pull/1297?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance)  
  
```diff  
@@            Coverage Diff            @@  
##           develop   #1297     +/-   ##  
=========================================  
+ Coverage     98.9%   99.0%   +0.1%       
=========================================  
  Files          270     270               
  Lines        17956   17910     -46       
  Branches      1998    1973     -25       
=========================================  
- Hits         17742   17714     -28       
+ Misses         214     196     -18       
```  
  
| [Files with missing lines](https://app.codecov.io/gh/cppalliance/decimal/pull/1297?dropdown=coverage&src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance) | Coverage Δ | |  
|---|---|---|  
| [include/boost/decimal/detail/add\_impl.hpp](https://app.codecov.io/gh/cppalliance/decimal/pull/1297?src=pr&el=tree&filepath=include%2Fboost%2Fdecimal%2Fdetail%2Fadd_impl.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance#diff-aW5jbHVkZS9ib29zdC9kZWNpbWFsL2RldGFpbC9hZGRfaW1wbC5ocHA=) | `98.0% <ø> (+12.5%)` | :arrow_up: |  
| [include/boost/decimal/detail/cmath/fma.hpp](https://app.codecov.io/gh/cppalliance/decimal/pull/1297?src=pr&el=tree&filepath=include%2Fboost%2Fdecimal%2Fdetail%2Fcmath%2Ffma.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance#diff-aW5jbHVkZS9ib29zdC9kZWNpbWFsL2RldGFpbC9jbWF0aC9mbWEuaHBw) | `100.0% <100.0%> (ø)` | |  
| [include/boost/decimal/detail/u256.hpp](https://app.codecov.io/gh/cppalliance/decimal/pull/1297?src=pr&el=tree&filepath=include%2Fboost%2Fdecimal%2Fdetail%2Fu256.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance#diff-aW5jbHVkZS9ib29zdC9kZWNpbWFsL2RldGFpbC91MjU2LmhwcA==) | `100.0% <100.0%> (ø)` | |  
  
... and [2 files with indirect coverage changes](https://app.codecov.io/gh/cppalliance/decimal/pull/1297/indirect-changes?src=pr&el=tree-more&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance)  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/cppalliance/decimal/pull/1297?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/cppalliance/decimal/pull/1297?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance). Last update [1def358...21f218e](https://app.codecov.io/gh/cppalliance/decimal/pull/1297?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance).  
</details>  
<details><summary> :rocket: New features to boost your workflow: </summary>  
  
- :snowflake: [Test Analytics](https://docs.codecov.com/docs/test-analytics): Detect flaky tests, report on failures, and find test suite problems.  
</details>

---
