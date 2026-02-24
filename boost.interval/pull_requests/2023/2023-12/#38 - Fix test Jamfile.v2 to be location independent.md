# #38 Fix test Jamfile.v2 to be location independent. [Closed]

> Username: grafikrobot  
> Created at: 2023-12-24 17:02:54 UTC  
> Updated at: 2024-07-28 13:20:38 UTC  
> Closed at: 2024-07-28 13:20:38 UTC  
> Url: https://github.com/boostorg/interval/pull/38  

In order to make the library transportable, specifically so we can support modular Boost distribution, we need to avoid encoding absolute paths in the monolithic Boost structure. This changes the test source reference to all be local to the project.

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2023-12-24 17:22:30 UTC  
> Updated_at: 2023-12-24 17:26:53 UTC  
> Url: https://github.com/boostorg/interval/pull/38#issuecomment-1868562273  

## [Codecov](https://app.codecov.io/gh/boostorg/interval/pull/38?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
All modified and coverable lines are covered by tests :white_check_mark:  
> Comparison is base [(`2eda741`)](https://app.codecov.io/gh/boostorg/interval/commit/2eda7413ac16dd4158005446438daf8a7e435dd9?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) 79.20% compared to head [(`ff05a1e`)](https://app.codecov.io/gh/boostorg/interval/pull/38?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) 79.20%.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/interval/pull/38/graphs/tree.svg?width=650&height=150&src=pr&token=GejkQK9wH8&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/interval/pull/38?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@           Coverage Diff            @@  
##           develop      #38   +/-   ##  
========================================  
  Coverage    79.20%   79.20%             
========================================  
  Files           22       22             
  Lines          933      933             
========================================  
  Hits           739      739             
  Misses         194      194             
```  
  
  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/interval/pull/38?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/interval/pull/38?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [2eda741...ff05a1e](https://app.codecov.io/gh/boostorg/interval/pull/38?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---

## Comment 2

> Username: grafikrobot  
> Created_at: 2024-01-14 06:11:46 UTC  
> Url: https://github.com/boostorg/interval/pull/38#issuecomment-1890859812  

FYI.. https://lists.boost.org/Archives/boost/2024/01/255704.php

---
