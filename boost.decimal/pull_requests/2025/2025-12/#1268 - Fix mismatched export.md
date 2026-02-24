# #1268 Fix mismatched export [Merged]

> Username: mborland  
> Created at: 2025-12-04 14:45:27 UTC  
> Updated at: 2025-12-04 17:42:32 UTC  
> Merged at: 2025-12-04 17:42:20 UTC  
> Closed at: 2025-12-04 17:42:20 UTC  
> Url: https://github.com/boostorg/decimal/pull/1268  

Need `export` on forward decl for module to work

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2025-12-04 17:40:08 UTC  
> Updated_at: 2025-12-04 17:42:32 UTC  
> Url: https://github.com/boostorg/decimal/pull/1268#issuecomment-3613490421  

## [Codecov](https://app.codecov.io/gh/cppalliance/decimal/pull/1268?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance) Report  
:white_check_mark: All modified and coverable lines are covered by tests.  
:white_check_mark: Project coverage is 98.9%. Comparing base ([`c76d539`](https://app.codecov.io/gh/cppalliance/decimal/commit/c76d539e01d5d4ba896229cae7f687880351b754?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance)) to head ([`2975dce`](https://app.codecov.io/gh/cppalliance/decimal/commit/2975dcec2218d8a8ea7af8f3771ffa6196c812ee?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance)).  
:warning: Report is 2 commits behind head on develop.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/cppalliance/decimal/pull/1268/graphs/tree.svg?width=650&height=150&src=pr&token=drvY8nnV5S&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance)](https://app.codecov.io/gh/cppalliance/decimal/pull/1268?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance)  
  
```diff  
@@            Coverage Diff            @@  
##           develop   #1268     +/-   ##  
=========================================  
- Coverage     98.9%   98.9%   -0.0%       
=========================================  
  Files          266     266               
  Lines        17698   17698               
  Branches      1970    1970               
=========================================  
- Hits         17492   17491      -1       
- Misses         206     207      +1       
```  
  
| [Files with missing lines](https://app.codecov.io/gh/cppalliance/decimal/pull/1268?dropdown=coverage&src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance) | Coverage Δ | |  
|---|---|---|  
| [...nclude/boost/decimal/detail/cmath/comparetotal.hpp](https://app.codecov.io/gh/cppalliance/decimal/pull/1268?src=pr&el=tree&filepath=include%2Fboost%2Fdecimal%2Fdetail%2Fcmath%2Fcomparetotal.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance#diff-aW5jbHVkZS9ib29zdC9kZWNpbWFsL2RldGFpbC9jbWF0aC9jb21wYXJldG90YWwuaHBw) | `73.1% <ø> (ø)` | |  
  
... and [1 file with indirect coverage changes](https://app.codecov.io/gh/cppalliance/decimal/pull/1268/indirect-changes?src=pr&el=tree-more&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance)  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/cppalliance/decimal/pull/1268?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/cppalliance/decimal/pull/1268?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance). Last update [c76d539...2975dce](https://app.codecov.io/gh/cppalliance/decimal/pull/1268?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance).  
</details>  
<details><summary> :rocket: New features to boost your workflow: </summary>  
  
- :snowflake: [Test Analytics](https://docs.codecov.com/docs/test-analytics): Detect flaky tests, report on failures, and find test suite problems.  
</details>

---
