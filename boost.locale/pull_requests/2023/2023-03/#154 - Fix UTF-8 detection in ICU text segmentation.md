# #154 Fix UTF-8 detection in ICU text segmentation [Merged]

> Username: Flamefire  
> Created at: 2023-03-07 19:24:33 UTC  
> Updated at: 2023-03-08 16:43:12 UTC  
> Merged at: 2023-03-08 16:43:08 UTC  
> Closed at: 2023-03-08 16:43:08 UTC  
> Url: https://github.com/boostorg/locale/pull/154  

Allow other spellings of `"UTF-8"` by unifying the encoding first. Fixes #44

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2023-03-08 08:51:23 UTC  
> Url: https://github.com/boostorg/locale/pull/154#issuecomment-1459768857  

# [Codecov](https://codecov.io/gh/boostorg/locale/pull/154?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#154](https://codecov.io/gh/boostorg/locale/pull/154?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (f5f2be7) into [develop](https://codecov.io/gh/boostorg/locale/commit/958ca63e4f0e4fd6fcd60378de63d38983866f49?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (958ca63) will **not change** coverage.  
> The diff coverage is `100.00%`.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/locale/pull/154/graphs/tree.svg?width=650&height=150&src=pr&token=vF6mWvgGUn&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://codecov.io/gh/boostorg/locale/pull/154?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@           Coverage Diff            @@  
##           develop     #154   +/-   ##  
========================================  
  Coverage    89.44%   89.44%             
========================================  
  Files          109      109             
  Lines         9457     9457             
========================================  
  Hits          8459     8459             
  Misses         998      998             
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/locale/pull/154?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [src/boost/locale/icu/boundary.cpp](https://codecov.io/gh/boostorg/locale/pull/154?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-c3JjL2Jvb3N0L2xvY2FsZS9pY3UvYm91bmRhcnkuY3Bw) | `88.23% <100.00%> (ø)` | |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/locale/pull/154?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/locale/pull/154?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [958ca63...f5f2be7](https://codecov.io/gh/boostorg/locale/pull/154?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---
