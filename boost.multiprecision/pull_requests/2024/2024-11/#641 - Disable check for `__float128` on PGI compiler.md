# #641 Disable check for `__float128` on PGI compiler [Merged]

> Username: mborland  
> Created at: 2024-11-04 17:13:39 UTC  
> Updated at: 2024-11-06 13:09:00 UTC  
> Merged at: 2024-11-06 13:08:44 UTC  
> Closed at: 2024-11-06 13:08:44 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/641  

Fixes #640   
  
Per config we should not have 128-bit float on this platform: https://github.com/boostorg/config/blob/19701e05d4832d1e97e06e791f2e4e1c7cba9b13/include/boost/config/compiler/pgi.hpp#L19.  
  
Not sure if anything has happened in the 7 years since that commit was last updated...

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2024-11-04 17:47:25 UTC  
> Updated_at: 2024-11-06 13:09:00 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/641#issuecomment-2455341139  

## [Codecov](https://app.codecov.io/gh/boostorg/multiprecision/pull/641?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
All modified and coverable lines are covered by tests :white_check_mark:  
> Project coverage is 94.1%. Comparing base [(`0ab75f9`)](https://app.codecov.io/gh/boostorg/multiprecision/commit/0ab75f95ab360b792fa9bdb7a5a9a573ebfe3747?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) to head [(`5108a96`)](https://app.codecov.io/gh/boostorg/multiprecision/commit/5108a961f3d7f04663ce75f141759faf24badc86?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> Report is 3 commits behind head on develop.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/multiprecision/pull/641/graphs/tree.svg?width=650&height=150&src=pr&token=SDDBym7Pc9&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/multiprecision/pull/641?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@            Coverage Diff            @@  
##           develop    #641     +/-   ##  
=========================================  
+ Coverage     92.5%   94.1%   +1.6%       
=========================================  
  Files          254     279     +25       
  Lines        23475   28885   +5410       
=========================================  
+ Hits         21699   27161   +5462       
+ Misses        1776    1724     -52       
```  
  
| [Files with missing lines](https://app.codecov.io/gh/boostorg/multiprecision/pull/641?dropdown=coverage&src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [...nclude/boost/multiprecision/detail/number\_base.hpp](https://app.codecov.io/gh/boostorg/multiprecision/pull/641?src=pr&el=tree&filepath=include%2Fboost%2Fmultiprecision%2Fdetail%2Fnumber_base.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9tdWx0aXByZWNpc2lvbi9kZXRhaWwvbnVtYmVyX2Jhc2UuaHBw) | `98.0% <ø> (+0.2%)` | :arrow_up: |  
  
... and [165 files with indirect coverage changes](https://app.codecov.io/gh/boostorg/multiprecision/pull/641/indirect-changes?src=pr&el=tree-more&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/multiprecision/pull/641?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/multiprecision/pull/641?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [0ab75f9...5108a96](https://app.codecov.io/gh/boostorg/multiprecision/pull/641?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---

## Comment 2

> Username: mborland  
> Created_at: 2024-11-06 13:07:33 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/641#issuecomment-2459708829  

The 3 failed run in circle all provide 0 information as to the failure, it's all build killed. Since GitHub actions and drone both show no problems it's likely safe to merge this. The issue reporter also says it fixed their problem.

---
