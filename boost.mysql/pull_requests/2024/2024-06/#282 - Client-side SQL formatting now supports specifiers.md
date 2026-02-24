# #282 Client-side SQL formatting now supports specifiers [Merged]

> Username: anarthal  
> Created at: 2024-06-15 16:20:14 UTC  
> Updated at: 2024-06-17 19:53:08 UTC  
> Merged at: 2024-06-17 19:53:08 UTC  
> Closed at: 2024-06-17 19:53:08 UTC  
> Url: https://github.com/boostorg/mysql/pull/282  

Removed the identifier class  
  
close #217

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2024-06-15 19:06:18 UTC  
> Updated_at: 2024-06-17 19:07:36 UTC  
> Url: https://github.com/boostorg/mysql/pull/282#issuecomment-2170534826  

## [Codecov](https://app.codecov.io/gh/boostorg/mysql/pull/282?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
Attention: Patch coverage is `99.04762%` with `1 line` in your changes missing coverage. Please review.  
> Project coverage is 98.50%. Comparing base [(`9ba4f9d`)](https://app.codecov.io/gh/boostorg/mysql/commit/9ba4f9dfed1fb3d2689cbdefbd0a7547876f8469?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) to head [(`bc0c4e2`)](https://app.codecov.io/gh/boostorg/mysql/commit/bc0c4e24d299418b0b690d296ddc853ba0239bfb?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/mysql/pull/282/graphs/tree.svg?width=650&height=150&src=pr&token=ajbbjm3OiO&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/mysql/pull/282?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #282      +/-   ##  
===========================================  
+ Coverage    98.46%   98.50%   +0.03%       
===========================================  
  Files          136      136                
  Lines         6502     6537      +35       
===========================================  
+ Hits          6402     6439      +37       
+ Misses         100       98       -2       
```  
  
| [Files](https://app.codecov.io/gh/boostorg/mysql/pull/282?dropdown=coverage&src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/mysql/detail/format\_sql.hpp](https://app.codecov.io/gh/boostorg/mysql/pull/282?src=pr&el=tree&filepath=include%2Fboost%2Fmysql%2Fdetail%2Fformat_sql.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9teXNxbC9kZXRhaWwvZm9ybWF0X3NxbC5ocHA=) | `100.00% <100.00%> (ø)` | |  
| [include/boost/mysql/format\_sql.hpp](https://app.codecov.io/gh/boostorg/mysql/pull/282?src=pr&el=tree&filepath=include%2Fboost%2Fmysql%2Fformat_sql.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9teXNxbC9mb3JtYXRfc3FsLmhwcA==) | `100.00% <100.00%> (ø)` | |  
| [include/boost/mysql/impl/error\_categories.ipp](https://app.codecov.io/gh/boostorg/mysql/pull/282?src=pr&el=tree&filepath=include%2Fboost%2Fmysql%2Fimpl%2Ferror_categories.ipp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9teXNxbC9pbXBsL2Vycm9yX2NhdGVnb3JpZXMuaXBw) | `97.05% <100.00%> (+6.14%)` | :arrow_up: |  
| [include/boost/mysql/impl/format\_sql.ipp](https://app.codecov.io/gh/boostorg/mysql/pull/282?src=pr&el=tree&filepath=include%2Fboost%2Fmysql%2Fimpl%2Fformat_sql.ipp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9teXNxbC9pbXBsL2Zvcm1hdF9zcWwuaXBw) | `99.08% <98.83%> (-0.39%)` | :arrow_down: |  
  
... and [1 file with indirect coverage changes](https://app.codecov.io/gh/boostorg/mysql/pull/282/indirect-changes?src=pr&el=tree-more&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/mysql/pull/282?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/mysql/pull/282?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [9ba4f9d...bc0c4e2](https://app.codecov.io/gh/boostorg/mysql/pull/282?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---
