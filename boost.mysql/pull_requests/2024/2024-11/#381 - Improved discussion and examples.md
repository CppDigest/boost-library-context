# #381 Improved discussion and examples [Merged]

> Username: anarthal  
> Created at: 2024-11-16 22:16:39 UTC  
> Updated at: 2024-11-29 16:47:52 UTC  
> Merged at: 2024-11-29 16:47:44 UTC  
> Closed at: 2024-11-29 16:47:44 UTC  
> Url: https://github.com/boostorg/mysql/pull/381  

Added a tutorial on UPDATEs, transactions and multi-queries  
Added a tutorial on connection_pool  
Added a tutorial on error handling  
Added examples on INSERTs and DELETEs  
Rewrote the discussion page on character sets  
Added a discussion page on the templated connection class  
Removed superseded examples on timeouts and multi-queries  
Updated the coverage build to gcc-14 (gcc-13 was using a non-LTS release that caused problems)  
  
Contributes to #365 and #366

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2024-11-29 10:58:48 UTC  
> Updated_at: 2024-11-29 16:47:52 UTC  
> Url: https://github.com/boostorg/mysql/pull/381#issuecomment-2507573509  

## [Codecov](https://app.codecov.io/gh/boostorg/mysql/pull/381?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
All modified and coverable lines are covered by tests :white_check_mark:  
> Project coverage is 98.95%. Comparing base [(`ef9224c`)](https://app.codecov.io/gh/boostorg/mysql/commit/ef9224c0ac396c4f1cd41bd41ce21ac3fb485f4f?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) to head [(`cb39a2c`)](https://app.codecov.io/gh/boostorg/mysql/commit/cb39a2cc7f972c93ffe0bca680b45c7dcdd250b9?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> Report is 1 commits behind head on develop.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/mysql/pull/381/graphs/tree.svg?width=650&height=150&src=pr&token=ajbbjm3OiO&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/mysql/pull/381?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@           Coverage Diff            @@  
##           develop     #381   +/-   ##  
========================================  
  Coverage    98.95%   98.95%             
========================================  
  Files          142      142             
  Lines         7185     7186    +1       
========================================  
+ Hits          7110     7111    +1       
  Misses          75       75             
```  
  
[see 1 file with indirect coverage changes](https://app.codecov.io/gh/boostorg/mysql/pull/381/indirect-changes?src=pr&el=tree-more&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/mysql/pull/381?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/mysql/pull/381?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [ef9224c...cb39a2c](https://app.codecov.io/gh/boostorg/mysql/pull/381?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---
