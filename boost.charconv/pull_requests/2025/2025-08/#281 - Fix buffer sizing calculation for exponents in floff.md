# #281 Fix buffer sizing calculation for exponents in floff [Merged]

> Username: mborland  
> Created at: 2025-08-14 11:07:47 UTC  
> Updated at: 2025-08-14 17:14:51 UTC  
> Merged at: 2025-08-14 11:57:06 UTC  
> Closed at: 2025-08-14 11:57:06 UTC  
> Url: https://github.com/boostorg/charconv/pull/281  

Closes: #280

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2025-08-14 11:53:43 UTC  
> Updated_at: 2025-08-14 17:14:51 UTC  
> Url: https://github.com/boostorg/charconv/pull/281#issuecomment-3188183139  

## [Codecov](https://app.codecov.io/gh/boostorg/charconv/pull/281?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
:white_check_mark: All modified and coverable lines are covered by tests.  
:white_check_mark: Project coverage is 94.90%. Comparing base ([`3554bc1`](https://app.codecov.io/gh/boostorg/charconv/commit/3554bc1008cd27e3f0918e1cea1d55959ad28906?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)) to head ([`5aaf6cb`](https://app.codecov.io/gh/boostorg/charconv/commit/5aaf6cbd3ef31a83d9b12500217cc75dd8c00124?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)).  
:warning: Report is 3 commits behind head on develop.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/charconv/pull/281/graphs/tree.svg?width=650&height=150&src=pr&token=jTnxtbJoTv&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/charconv/pull/281?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #281      +/-   ##  
===========================================  
+ Coverage    94.89%   94.90%   +0.01%       
===========================================  
  Files           71       72       +1       
  Lines         9123     9147      +24       
===========================================  
+ Hits          8657     8681      +24       
  Misses         466      466                
```  
  
| [Files with missing lines](https://app.codecov.io/gh/boostorg/charconv/pull/281?dropdown=coverage&src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/charconv/detail/dragonbox/floff.hpp](https://app.codecov.io/gh/boostorg/charconv/pull/281?src=pr&el=tree&filepath=include%2Fboost%2Fcharconv%2Fdetail%2Fdragonbox%2Ffloff.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9jaGFyY29udi9kZXRhaWwvZHJhZ29uYm94L2Zsb2ZmLmhwcA==) | `87.24% <100.00%> (-0.03%)` | :arrow_down: |  
| [test/github\_issue\_280.cpp](https://app.codecov.io/gh/boostorg/charconv/pull/281?src=pr&el=tree&filepath=test%2Fgithub_issue_280.cpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC9naXRodWJfaXNzdWVfMjgwLmNwcA==) | `100.00% <100.00%> (ø)` | |  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/charconv/pull/281?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/charconv/pull/281?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [3554bc1...5aaf6cb](https://app.codecov.io/gh/boostorg/charconv/pull/281?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
</details>  
<details><summary> :rocket: New features to boost your workflow: </summary>  
  
- :snowflake: [Test Analytics](https://docs.codecov.com/docs/test-analytics): Detect flaky tests, report on failures, and find test suite problems.  
</details>

---
