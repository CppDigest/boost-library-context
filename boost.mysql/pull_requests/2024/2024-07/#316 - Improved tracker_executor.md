# #316 Improved tracker_executor [Merged]

> Username: anarthal  
> Created at: 2024-07-14 21:50:11 UTC  
> Updated at: 2024-07-15 10:26:49 UTC  
> Merged at: 2024-07-15 10:26:48 UTC  
> Closed at: 2024-07-15 10:26:48 UTC  
> Url: https://github.com/boostorg/mysql/pull/316  

Added the ability to detect the executor call stack and whether we're running from an initiating function or not.  
Integration tests now consistently check these two.

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2024-07-15 09:16:09 UTC  
> Updated_at: 2024-07-15 09:36:03 UTC  
> Url: https://github.com/boostorg/mysql/pull/316#issuecomment-2228043709  

## [Codecov](https://app.codecov.io/gh/boostorg/mysql/pull/316?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
All modified and coverable lines are covered by tests :white_check_mark:  
> Project coverage is 98.33%. Comparing base [(`c3337d2`)](https://app.codecov.io/gh/boostorg/mysql/commit/c3337d20d4232363b2e7b36d4656065b5ca430af?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) to head [(`413e163`)](https://app.codecov.io/gh/boostorg/mysql/commit/413e163f936f53b0d11ab839dc4292c3279b2c52?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/mysql/pull/316/graphs/tree.svg?width=650&height=150&src=pr&token=ajbbjm3OiO&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/mysql/pull/316?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #316      +/-   ##  
===========================================  
- Coverage    98.33%   98.33%   -0.01%       
===========================================  
  Files          129      128       -1       
  Lines         5895     5887       -8       
===========================================  
- Hits          5797     5789       -8       
  Misses          98       98                
```  
  
[see 2 files with indirect coverage changes](https://app.codecov.io/gh/boostorg/mysql/pull/316/indirect-changes?src=pr&el=tree-more&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/mysql/pull/316?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/mysql/pull/316?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [c3337d2...413e163](https://app.codecov.io/gh/boostorg/mysql/pull/316?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---
