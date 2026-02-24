# #166 CI: Add coverage job for Windows [Closed]

> Username: Flamefire  
> Created at: 2023-05-03 15:54:49 UTC  
> Updated at: 2023-05-04 16:23:34 UTC  
> Closed at: 2023-05-04 16:23:31 UTC  
> Url: https://github.com/boostorg/locale/pull/166  

There are some Windows-specific codeparts for which coverage information isn't currently available.  
So add one job to get information about that.

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2023-05-04 00:04:08 UTC  
> Updated_at: 2023-05-04 00:57:01 UTC  
> Url: https://github.com/boostorg/locale/pull/166#issuecomment-1533898656  

## [Codecov](https://app.codecov.io/gh/boostorg/locale/pull/166?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#166](https://app.codecov.io/gh/boostorg/locale/pull/166?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (108a8ed) into [develop](https://app.codecov.io/gh/boostorg/locale/commit/0daace933ca41a32763735b570bc1429cdbbb935?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (0daace9) will **increase** coverage by `0.00%`.  
> The diff coverage is `n/a`.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/locale/pull/166/graphs/tree.svg?width=650&height=150&src=pr&token=vF6mWvgGUn&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/locale/pull/166?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@           Coverage Diff            @@  
##           develop     #166   +/-   ##  
========================================  
  Coverage    90.69%   90.69%             
========================================  
  Files          111      111             
  Lines         9654     9655    +1       
========================================  
+ Hits          8756     8757    +1       
  Misses         898      898             
```  
  
  
[see 1 file with indirect coverage changes](https://app.codecov.io/gh/boostorg/locale/pull/166/indirect-changes?src=pr&el=tree-more&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/locale/pull/166?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/locale/pull/166?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [0daace9...108a8ed](https://app.codecov.io/gh/boostorg/locale/pull/166?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---

## Comment 2

> Username: Flamefire  
> Created_at: 2023-05-04 16:23:31 UTC  
> Url: https://github.com/boostorg/locale/pull/166#issuecomment-1535037680  

Already done on GHA: https://github.com/boostorg/locale/blob/0daace933ca41a32763735b570bc1429cdbbb935/.github/workflows/ci.yml#L322

---
