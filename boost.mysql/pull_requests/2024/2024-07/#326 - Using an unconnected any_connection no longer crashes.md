# #326 Using an unconnected any_connection no longer crashes [Merged]

> Username: anarthal  
> Created at: 2024-07-26 20:22:29 UTC  
> Updated at: 2024-07-29 11:37:03 UTC  
> Merged at: 2024-07-29 11:37:01 UTC  
> Closed at: 2024-07-29 11:37:01 UTC  
> Url: https://github.com/boostorg/mysql/pull/326  

Refactored `variant_stream` and added tests  
  
close #230   
close #321

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2024-07-29 10:51:35 UTC  
> Url: https://github.com/boostorg/mysql/pull/326#issuecomment-2255609134  

## [Codecov](https://app.codecov.io/gh/boostorg/mysql/pull/326?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
All modified and coverable lines are covered by tests :white_check_mark:  
> Project coverage is 98.68%. Comparing base [(`b0c2639`)](https://app.codecov.io/gh/boostorg/mysql/commit/b0c2639dbb8ea49b651c96ee18c04f5e3b047844?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) to head [(`5bb3d1c`)](https://app.codecov.io/gh/boostorg/mysql/commit/5bb3d1c8fa73e112ae6d9324598f8e56282b8e20?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/mysql/pull/326/graphs/tree.svg?width=650&height=150&src=pr&token=ajbbjm3OiO&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/mysql/pull/326?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #326      +/-   ##  
===========================================  
+ Coverage    98.51%   98.68%   +0.17%       
===========================================  
  Files          138      138                
  Lines         7058     7069      +11       
===========================================  
+ Hits          6953     6976      +23       
+ Misses         105       93      -12       
```  
  
| [Files](https://app.codecov.io/gh/boostorg/mysql/pull/326?dropdown=coverage&src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [...clude/boost/mysql/impl/internal/variant\_stream.hpp](https://app.codecov.io/gh/boostorg/mysql/pull/326?src=pr&el=tree&filepath=include%2Fboost%2Fmysql%2Fimpl%2Finternal%2Fvariant_stream.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9teXNxbC9pbXBsL2ludGVybmFsL3ZhcmlhbnRfc3RyZWFtLmhwcA==) | `100.00% <100.00%> (+9.82%)` | :arrow_up: |  
  
... and [1 file with indirect coverage changes](https://app.codecov.io/gh/boostorg/mysql/pull/326/indirect-changes?src=pr&el=tree-more&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/mysql/pull/326?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/mysql/pull/326?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [b0c2639...5bb3d1c](https://app.codecov.io/gh/boostorg/mysql/pull/326?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---
