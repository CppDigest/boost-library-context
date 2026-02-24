# #158 Fix UB on invalid index in format string [Merged]

> Username: Flamefire  
> Created at: 2023-04-07 18:52:25 UTC  
> Updated at: 2023-04-09 15:50:53 UTC  
> Merged at: 2023-04-09 15:50:46 UTC  
> Closed at: 2023-04-09 15:50:46 UTC  
> Url: https://github.com/boostorg/locale/pull/158  



---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2023-04-09 01:01:27 UTC  
> Url: https://github.com/boostorg/locale/pull/158#issuecomment-1501010581  

## [Codecov](https://codecov.io/gh/boostorg/locale/pull/158?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#158](https://codecov.io/gh/boostorg/locale/pull/158?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (d6e6071) into [develop](https://codecov.io/gh/boostorg/locale/commit/a8e3a23c53ae8f59e298320d1b6a63be29fe1a12?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (a8e3a23) will **increase** coverage by `0.07%`.  
> The diff coverage is `95.91%`.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/locale/pull/158/graphs/tree.svg?width=650&height=150&src=pr&token=vF6mWvgGUn&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://codecov.io/gh/boostorg/locale/pull/158?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #158      +/-   ##  
===========================================  
+ Coverage    89.44%   89.52%   +0.07%       
===========================================  
  Files          109      109                
  Lines         9457     9534      +77       
===========================================  
+ Hits          8459     8535      +76       
- Misses         998      999       +1       
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/locale/pull/158?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/locale/format.hpp](https://codecov.io/gh/boostorg/locale/pull/158?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9sb2NhbGUvZm9ybWF0LmhwcA==) | `94.32% <ø> (ø)` | |  
| [src/boost/locale/shared/format.cpp](https://codecov.io/gh/boostorg/locale/pull/158?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-c3JjL2Jvb3N0L2xvY2FsZS9zaGFyZWQvZm9ybWF0LmNwcA==) | `83.62% <83.33%> (-1.60%)` | :arrow_down: |  
| [src/boost/locale/std/std\_backend.cpp](https://codecov.io/gh/boostorg/locale/pull/158?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-c3JjL2Jvb3N0L2xvY2FsZS9zdGQvc3RkX2JhY2tlbmQuY3Bw) | `82.85% <92.59%> (-0.64%)` | :arrow_down: |  
| [src/boost/locale/util/default\_locale.cpp](https://codecov.io/gh/boostorg/locale/pull/158?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-c3JjL2Jvb3N0L2xvY2FsZS91dGlsL2RlZmF1bHRfbG9jYWxlLmNwcA==) | `92.59% <100.00%> (ø)` | |  
| [src/boost/locale/util/numeric.hpp](https://codecov.io/gh/boostorg/locale/pull/158?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-c3JjL2Jvb3N0L2xvY2FsZS91dGlsL251bWVyaWMuaHBw) | `79.28% <100.00%> (+1.58%)` | :arrow_up: |  
| [test/test\_formatting.cpp](https://codecov.io/gh/boostorg/locale/pull/158?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC90ZXN0X2Zvcm1hdHRpbmcuY3Bw) | `99.68% <100.00%> (+<0.01%)` | :arrow_up: |  
| [test/test\_util.cpp](https://codecov.io/gh/boostorg/locale/pull/158?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC90ZXN0X3V0aWwuY3Bw) | `100.00% <100.00%> (ø)` | |  
  
... and [3 files with indirect coverage changes](https://codecov.io/gh/boostorg/locale/pull/158/indirect-changes?src=pr&el=tree-more&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://codecov.io/gh/boostorg/locale/pull/158?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/locale/pull/158?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [a8e3a23...d6e6071](https://codecov.io/gh/boostorg/locale/pull/158?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---
