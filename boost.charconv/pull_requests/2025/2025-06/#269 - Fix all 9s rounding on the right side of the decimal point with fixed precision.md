# #269 Fix all 9s rounding on the right side of the decimal point with fixed precision [Merged]

> Username: mborland  
> Created at: 2025-06-16 18:51:11 UTC  
> Updated at: 2025-06-16 20:07:38 UTC  
> Merged at: 2025-06-16 20:07:26 UTC  
> Closed at: 2025-06-16 20:07:26 UTC  
> Url: https://github.com/boostorg/charconv/pull/269  

Closes: #267

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2025-06-16 19:29:52 UTC  
> Updated_at: 2025-06-16 20:07:38 UTC  
> Url: https://github.com/boostorg/charconv/pull/269#issuecomment-2977842749  

## [Codecov](https://app.codecov.io/gh/boostorg/charconv/pull/269?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
All modified and coverable lines are covered by tests :white_check_mark:  
> Project coverage is 94.86%. Comparing base [(`38854b2`)](https://app.codecov.io/gh/boostorg/charconv/commit/38854b2cfbe42fcac21928a9b4c8611a38374df6?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) to head [(`ba6a62d`)](https://app.codecov.io/gh/boostorg/charconv/commit/ba6a62d34226e249bb4a52b20533be2434bed113?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> Report is 4 commits behind head on develop.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/charconv/pull/269/graphs/tree.svg?width=650&height=150&src=pr&token=jTnxtbJoTv&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/charconv/pull/269?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #269      +/-   ##  
===========================================  
+ Coverage    94.85%   94.86%   +0.01%       
===========================================  
  Files           69       70       +1       
  Lines         9077     9097      +20       
===========================================  
+ Hits          8610     8630      +20       
  Misses         467      467                
```  
  
| [Files with missing lines](https://app.codecov.io/gh/boostorg/charconv/pull/269?dropdown=coverage&src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/charconv/detail/dragonbox/floff.hpp](https://app.codecov.io/gh/boostorg/charconv/pull/269?src=pr&el=tree&filepath=include%2Fboost%2Fcharconv%2Fdetail%2Fdragonbox%2Ffloff.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9jaGFyY29udi9kZXRhaWwvZHJhZ29uYm94L2Zsb2ZmLmhwcA==) | `87.10% <100.00%> (ø)` | |  
| [test/github\_issue\_267.cpp](https://app.codecov.io/gh/boostorg/charconv/pull/269?src=pr&el=tree&filepath=test%2Fgithub_issue_267.cpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC9naXRodWJfaXNzdWVfMjY3LmNwcA==) | `100.00% <100.00%> (ø)` | |  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/charconv/pull/269?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/charconv/pull/269?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [38854b2...ba6a62d](https://app.codecov.io/gh/boostorg/charconv/pull/269?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>  
<details><summary> :rocket: New features to boost your workflow: </summary>  
  
- :snowflake: [Test Analytics](https://docs.codecov.com/docs/test-analytics): Detect flaky tests, report on failures, and find test suite problems.  
</details>

---
