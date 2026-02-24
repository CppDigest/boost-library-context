# #357 Removed global_io_context [Merged]

> Username: anarthal  
> Created at: 2024-09-28 14:31:01 UTC  
> Updated at: 2024-09-28 17:39:05 UTC  
> Merged at: 2024-09-28 17:36:43 UTC  
> Closed at: 2024-09-28 17:36:43 UTC  
> Url: https://github.com/boostorg/mysql/pull/357  

Now tests are independent of each other  
Refactor common utilities for separate compilation

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2024-09-28 17:26:11 UTC  
> Updated_at: 2024-09-28 17:39:05 UTC  
> Url: https://github.com/boostorg/mysql/pull/357#issuecomment-2380841073  

## [Codecov](https://app.codecov.io/gh/boostorg/mysql/pull/357?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
All modified and coverable lines are covered by tests :white_check_mark:  
> Project coverage is 99.05%. Comparing base [(`e72b490`)](https://app.codecov.io/gh/boostorg/mysql/commit/e72b4906af2a3695b8aff94c1ad27c13545e98d0?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) to head [(`8e8392a`)](https://app.codecov.io/gh/boostorg/mysql/commit/8e8392a6661275fb05a91abf33ae3b21d98f0d93?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> Report is 1 commits behind head on develop.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/mysql/pull/357/graphs/tree.svg?width=650&height=150&src=pr&token=ajbbjm3OiO&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/mysql/pull/357?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #357      +/-   ##  
===========================================  
- Coverage    99.05%   99.05%   -0.01%       
===========================================  
  Files          143      143                
  Lines         7185     7184       -1       
===========================================  
- Hits          7117     7116       -1       
  Misses          68       68                
```  
  
[see 1 file with indirect coverage changes](https://app.codecov.io/gh/boostorg/mysql/pull/357/indirect-changes?src=pr&el=tree-more&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/mysql/pull/357?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/mysql/pull/357?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [e72b490...8e8392a](https://app.codecov.io/gh/boostorg/mysql/pull/357?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---
