# #195 Fix handling of escaped opening brace in format strings [Merged]

> Username: Flamefire  
> Created at: 2023-09-19 08:28:54 UTC  
> Updated at: 2023-09-20 14:17:29 UTC  
> Merged at: 2023-09-20 14:17:25 UTC  
> Closed at: 2023-09-20 14:17:25 UTC  
> Url: https://github.com/boostorg/locale/pull/195  

When using an escaped opening brace in a format string it was output 2 times instead of one.  
Fix and add test  
  
Fixes #194

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2023-09-19 14:00:42 UTC  
> Updated_at: 2023-09-20 11:39:52 UTC  
> Url: https://github.com/boostorg/locale/pull/195#issuecomment-1725650751  

## [Codecov](https://app.codecov.io/gh/boostorg/locale/pull/195?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#195](https://app.codecov.io/gh/boostorg/locale/pull/195?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (744799a) into [develop](https://app.codecov.io/gh/boostorg/locale/commit/e1b20caa87c6660019f89689ea5eed20c061358e?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (e1b20ca) will **increase** coverage by `0.10%`.  
> The diff coverage is `100.00%`.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/locale/pull/195/graphs/tree.svg?width=650&height=150&src=pr&token=vF6mWvgGUn&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/locale/pull/195?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #195      +/-   ##  
===========================================  
+ Coverage    94.82%   94.92%   +0.10%       
===========================================  
  Files          114      114                
  Lines         9878     9917      +39       
===========================================  
+ Hits          9367     9414      +47       
+ Misses         511      503       -8       
```  
  
  
| [Files Changed](https://app.codecov.io/gh/boostorg/locale/pull/195?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/locale/format.hpp](https://app.codecov.io/gh/boostorg/locale/pull/195?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9sb2NhbGUvZm9ybWF0LmhwcA==) | `97.82% <100.00%> (+0.01%)` | :arrow_up: |  
| [test/test\_formatting.cpp](https://app.codecov.io/gh/boostorg/locale/pull/195?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC90ZXN0X2Zvcm1hdHRpbmcuY3Bw) | `100.00% <100.00%> (ø)` | |  
  
... and [2 files with indirect coverage changes](https://app.codecov.io/gh/boostorg/locale/pull/195/indirect-changes?src=pr&el=tree-more&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/locale/pull/195?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/locale/pull/195?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [e1b20ca...744799a](https://app.codecov.io/gh/boostorg/locale/pull/195?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---
