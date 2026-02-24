# #192 Fix internal crash in `floff` [Merged]

> Username: mborland  
> Created at: 2024-04-23 06:31:14 UTC  
> Updated at: 2024-04-23 07:15:22 UTC  
> Merged at: 2024-04-23 07:15:19 UTC  
> Closed at: 2024-04-23 07:15:19 UTC  
> Url: https://github.com/boostorg/charconv/pull/192  

Closes: #190   
  
Thanks @jk-jeon for the quick response.

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2024-04-23 07:08:48 UTC  
> Url: https://github.com/boostorg/charconv/pull/192#issuecomment-2071585036  

## [Codecov](https://app.codecov.io/gh/boostorg/charconv/pull/192?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
All modified and coverable lines are covered by tests :white_check_mark:  
> Project coverage is 94.01%. Comparing base [(`aa70adb`)](https://app.codecov.io/gh/boostorg/charconv/commit/aa70adb43899fac9197b028de286f5902e04c976?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) to head [(`fbf58ca`)](https://app.codecov.io/gh/boostorg/charconv/pull/192?dropdown=coverage&src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/charconv/pull/192/graphs/tree.svg?width=650&height=150&src=pr&token=jTnxtbJoTv&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/charconv/pull/192?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #192      +/-   ##  
===========================================  
+ Coverage    93.39%   94.01%   +0.62%       
===========================================  
  Files           66       66                
  Lines         8432     8439       +7       
===========================================  
+ Hits          7875     7934      +59       
+ Misses         557      505      -52       
```  
  
  
| [Files](https://app.codecov.io/gh/boostorg/charconv/pull/192?dropdown=coverage&src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/charconv/detail/dragonbox/floff.hpp](https://app.codecov.io/gh/boostorg/charconv/pull/192?src=pr&el=tree&filepath=include%2Fboost%2Fcharconv%2Fdetail%2Fdragonbox%2Ffloff.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9jaGFyY29udi9kZXRhaWwvZHJhZ29uYm94L2Zsb2ZmLmhwcA==) | `84.36% <100.00%> (+4.26%)` | :arrow_up: |  
| [test/github\_issue\_186.cpp](https://app.codecov.io/gh/boostorg/charconv/pull/192?src=pr&el=tree&filepath=test%2Fgithub_issue_186.cpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC9naXRodWJfaXNzdWVfMTg2LmNwcA==) | `100.00% <100.00%> (ø)` | |  
  
... and [2 files with indirect coverage changes](https://app.codecov.io/gh/boostorg/charconv/pull/192/indirect-changes?src=pr&el=tree-more&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/charconv/pull/192?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/charconv/pull/192?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [aa70adb...fbf58ca](https://app.codecov.io/gh/boostorg/charconv/pull/192?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---
