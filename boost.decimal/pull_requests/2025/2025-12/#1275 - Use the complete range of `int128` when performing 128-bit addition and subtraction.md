# #1275 Use the complete range of `int128` when performing 128-bit addition and subtraction [Merged]

> Username: mborland  
> Created at: 2025-12-08 10:34:41 UTC  
> Updated at: 2025-12-09 13:40:06 UTC  
> Merged at: 2025-12-09 13:39:58 UTC  
> Closed at: 2025-12-09 13:39:58 UTC  
> Url: https://github.com/boostorg/decimal/pull/1275  

Closes: #1260

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2025-12-09 13:38:07 UTC  
> Updated_at: 2025-12-09 13:40:06 UTC  
> Url: https://github.com/boostorg/decimal/pull/1275#issuecomment-3632316223  

## [Codecov](https://app.codecov.io/gh/cppalliance/decimal/pull/1275?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance) Report  
:white_check_mark: All modified and coverable lines are covered by tests.  
:white_check_mark: Project coverage is 99.0%. Comparing base ([`b5789e1`](https://app.codecov.io/gh/cppalliance/decimal/commit/b5789e18371cc388d16db3e5c93eb82bb94e1f2f?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance)) to head ([`3dfe776`](https://app.codecov.io/gh/cppalliance/decimal/commit/3dfe77679d52c204a610b8c3b95a1ceba09cafd6?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance)).  
:warning: Report is 5 commits behind head on develop.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/cppalliance/decimal/pull/1275/graphs/tree.svg?width=650&height=150&src=pr&token=drvY8nnV5S&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance)](https://app.codecov.io/gh/cppalliance/decimal/pull/1275?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance)  
  
```diff  
@@            Coverage Diff            @@  
##           develop   #1275     +/-   ##  
=========================================  
+ Coverage     99.0%   99.0%   +0.1%       
=========================================  
  Files          267     268      +1       
  Lines        17734   17746     +12       
  Branches      1981    1980      -1       
=========================================  
+ Hits         17542   17554     +12       
  Misses         192     192               
```  
  
| [Files with missing lines](https://app.codecov.io/gh/cppalliance/decimal/pull/1275?dropdown=coverage&src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance) | Coverage Δ | |  
|---|---|---|  
| [include/boost/decimal/detail/add\_impl.hpp](https://app.codecov.io/gh/cppalliance/decimal/pull/1275?src=pr&el=tree&filepath=include%2Fboost%2Fdecimal%2Fdetail%2Fadd_impl.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance#diff-aW5jbHVkZS9ib29zdC9kZWNpbWFsL2RldGFpbC9hZGRfaW1wbC5ocHA=) | `98.1% <100.0%> (ø)` | |  
| [test/github\_issue\_1260.cpp](https://app.codecov.io/gh/cppalliance/decimal/pull/1275?src=pr&el=tree&filepath=test%2Fgithub_issue_1260.cpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance#diff-dGVzdC9naXRodWJfaXNzdWVfMTI2MC5jcHA=) | `100.0% <100.0%> (ø)` | |  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/cppalliance/decimal/pull/1275?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/cppalliance/decimal/pull/1275?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance). Last update [b5789e1...3dfe776](https://app.codecov.io/gh/cppalliance/decimal/pull/1275?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance).  
</details>  
<details><summary> :rocket: New features to boost your workflow: </summary>  
  
- :snowflake: [Test Analytics](https://docs.codecov.com/docs/test-analytics): Detect flaky tests, report on failures, and find test suite problems.  
</details>

---
