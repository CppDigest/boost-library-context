# #1300 Fix non-finite handling in `fmax` and `fmin`  [Merged]

> Username: mborland  
> Created at: 2026-01-14 19:29:28 UTC  
> Updated at: 2026-01-14 22:40:31 UTC  
> Merged at: 2026-01-14 22:40:23 UTC  
> Closed at: 2026-01-14 22:40:23 UTC  
> Url: https://github.com/boostorg/decimal/pull/1300  

Closes: #1299

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2026-01-14 22:38:12 UTC  
> Updated_at: 2026-01-14 22:40:31 UTC  
> Url: https://github.com/boostorg/decimal/pull/1300#issuecomment-3752018548  

## [Codecov](https://app.codecov.io/gh/cppalliance/decimal/pull/1300?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance) Report  
:x: Patch coverage is `97.67442%` with `2 lines` in your changes missing coverage. Please review.  
:white_check_mark: Project coverage is 98.9%. Comparing base ([`322ffce`](https://app.codecov.io/gh/cppalliance/decimal/commit/322ffcebc8ab1221a8a65d491184bed36467cfa7?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance)) to head ([`ead1547`](https://app.codecov.io/gh/cppalliance/decimal/commit/ead15473ca69c580ceb8ec2bd97909806dd90bcc?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance)).  
:warning: Report is 6 commits behind head on develop.  
  
| [Files with missing lines](https://app.codecov.io/gh/cppalliance/decimal/pull/1300?dropdown=coverage&src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance) | Patch % | Lines |  
|---|---|---|  
| [test/github\_issue\_1299.cpp](https://app.codecov.io/gh/cppalliance/decimal/pull/1300?src=pr&el=tree&filepath=test%2Fgithub_issue_1299.cpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance#diff-dGVzdC9naXRodWJfaXNzdWVfMTI5OS5jcHA=) | 97.0% | [2 Missing :warning: ](https://app.codecov.io/gh/cppalliance/decimal/pull/1300?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance) |  
  
<details><summary>Additional details and impacted files</summary>  
  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/cppalliance/decimal/pull/1300/graphs/tree.svg?width=650&height=150&src=pr&token=drvY8nnV5S&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance)](https://app.codecov.io/gh/cppalliance/decimal/pull/1300?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance)  
  
```diff  
@@            Coverage Diff            @@  
##           develop   #1300     +/-   ##  
=========================================  
- Coverage     98.9%   98.9%   -0.0%       
=========================================  
  Files          270     271      +1       
  Lines        17607   17683     +76       
  Branches      1883    1886      +3       
=========================================  
+ Hits         17411   17486     +75       
- Misses         196     197      +1       
```  
  
| [Files with missing lines](https://app.codecov.io/gh/cppalliance/decimal/pull/1300?dropdown=coverage&src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance) | Coverage Δ | |  
|---|---|---|  
| [include/boost/decimal/detail/cmath/fmax.hpp](https://app.codecov.io/gh/cppalliance/decimal/pull/1300?src=pr&el=tree&filepath=include%2Fboost%2Fdecimal%2Fdetail%2Fcmath%2Ffmax.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance#diff-aW5jbHVkZS9ib29zdC9kZWNpbWFsL2RldGFpbC9jbWF0aC9mbWF4LmhwcA==) | `100.0% <100.0%> (ø)` | |  
| [include/boost/decimal/detail/cmath/fmin.hpp](https://app.codecov.io/gh/cppalliance/decimal/pull/1300?src=pr&el=tree&filepath=include%2Fboost%2Fdecimal%2Fdetail%2Fcmath%2Ffmin.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance#diff-aW5jbHVkZS9ib29zdC9kZWNpbWFsL2RldGFpbC9jbWF0aC9mbWluLmhwcA==) | `100.0% <100.0%> (+14.3%)` | :arrow_up: |  
| [include/boost/decimal/detail/cmath/nan.hpp](https://app.codecov.io/gh/cppalliance/decimal/pull/1300?src=pr&el=tree&filepath=include%2Fboost%2Fdecimal%2Fdetail%2Fcmath%2Fnan.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance#diff-aW5jbHVkZS9ib29zdC9kZWNpbWFsL2RldGFpbC9jbWF0aC9uYW4uaHBw) | `92.9% <100.0%> (+0.3%)` | :arrow_up: |  
| [test/github\_issue\_1299.cpp](https://app.codecov.io/gh/cppalliance/decimal/pull/1300?src=pr&el=tree&filepath=test%2Fgithub_issue_1299.cpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance#diff-dGVzdC9naXRodWJfaXNzdWVfMTI5OS5jcHA=) | `97.0% <97.0%> (ø)` | |  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/cppalliance/decimal/pull/1300?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/cppalliance/decimal/pull/1300?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance). Last update [322ffce...ead1547](https://app.codecov.io/gh/cppalliance/decimal/pull/1300?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance).  
</details>  
<details><summary> :rocket: New features to boost your workflow: </summary>  
  
- :snowflake: [Test Analytics](https://docs.codecov.com/docs/test-analytics): Detect flaky tests, report on failures, and find test suite problems.  
</details>

---
