# #108 Fix failing ICU tests [Merged]

> Username: Flamefire  
> Created at: 2022-09-11 12:15:37 UTC  
> Updated at: 2022-10-02 20:48:37 UTC  
> Merged at: 2022-10-02 20:48:33 UTC  
> Closed at: 2022-10-02 20:48:33 UTC  
> Url: https://github.com/boostorg/locale/pull/108  

Fix tests failing on recent ICU versions  
  
Fixes #69

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2022-09-11 16:43:46 UTC  
> Updated_at: 2022-10-02 14:03:00 UTC  
> Url: https://github.com/boostorg/locale/pull/108#issuecomment-1243000984  

# [Codecov](https://codecov.io/gh/boostorg/locale/pull/108?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#108](https://codecov.io/gh/boostorg/locale/pull/108?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (49663b6) into [develop](https://codecov.io/gh/boostorg/locale/commit/c6debb769c5acac4c796c13354b42311c0d4bc34?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (c6debb7) will **increase** coverage by `4.35%`.  
> The diff coverage is `98.03%`.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/locale/pull/108/graphs/tree.svg?width=650&height=150&src=pr&token=vF6mWvgGUn&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://codecov.io/gh/boostorg/locale/pull/108?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #108      +/-   ##  
===========================================  
+ Coverage    84.20%   88.55%   +4.35%       
===========================================  
  Files           73      100      +27       
  Lines         5794     9388    +3594       
===========================================  
+ Hits          4879     8314    +3435       
- Misses         915     1074     +159       
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/locale/pull/108?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [src/boost/locale/icu/boundary.cpp](https://codecov.io/gh/boostorg/locale/pull/108/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-c3JjL2Jvb3N0L2xvY2FsZS9pY3UvYm91bmRhcnkuY3Bw) | `81.41% <ø> (ø)` | |  
| [src/boost/locale/icu/formatter.cpp](https://codecov.io/gh/boostorg/locale/pull/108/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-c3JjL2Jvb3N0L2xvY2FsZS9pY3UvZm9ybWF0dGVyLmNwcA==) | `82.97% <75.00%> (ø)` | |  
| [src/boost/locale/icu/icu\_util.hpp](https://codecov.io/gh/boostorg/locale/pull/108/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-c3JjL2Jvb3N0L2xvY2FsZS9pY3UvaWN1X3V0aWwuaHBw) | `63.63% <80.00%> (ø)` | |  
| [src/boost/locale/icu/date\_time.cpp](https://codecov.io/gh/boostorg/locale/pull/108/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-c3JjL2Jvb3N0L2xvY2FsZS9pY3UvZGF0ZV90aW1lLmNwcA==) | `82.50% <100.00%> (ø)` | |  
| [src/boost/locale/icu/predefined\_formatters.hpp](https://codecov.io/gh/boostorg/locale/pull/108/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-c3JjL2Jvb3N0L2xvY2FsZS9pY3UvcHJlZGVmaW5lZF9mb3JtYXR0ZXJzLmhwcA==) | `92.00% <100.00%> (ø)` | |  
| [src/boost/locale/icu/time\_zone.cpp](https://codecov.io/gh/boostorg/locale/pull/108/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-c3JjL2Jvb3N0L2xvY2FsZS9pY3UvdGltZV96b25lLmNwcA==) | `100.00% <100.00%> (ø)` | |  
| [test/test\_boundary.cpp](https://codecov.io/gh/boostorg/locale/pull/108/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC90ZXN0X2JvdW5kYXJ5LmNwcA==) | `97.72% <100.00%> (-2.28%)` | :arrow_down: |  
| [test/test\_formatting.cpp](https://codecov.io/gh/boostorg/locale/pull/108/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC90ZXN0X2Zvcm1hdHRpbmcuY3Bw) | `100.00% <100.00%> (ø)` | |  
| [include/boost/locale/localization\_backend.hpp](https://codecov.io/gh/boostorg/locale/pull/108/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9sb2NhbGUvbG9jYWxpemF0aW9uX2JhY2tlbmQuaHBw) | `75.00% <0.00%> (-25.00%)` | :arrow_down: |  
| [src/boost/locale/util/default\_locale.cpp](https://codecov.io/gh/boostorg/locale/pull/108/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-c3JjL2Jvb3N0L2xvY2FsZS91dGlsL2RlZmF1bHRfbG9jYWxlLmNwcA==) | `67.85% <0.00%> (-23.06%)` | :arrow_down: |  
| ... and [76 more](https://codecov.io/gh/boostorg/locale/pull/108/diff?src=pr&el=tree-more&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/locale/pull/108?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/locale/pull/108?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [c6debb7...49663b6](https://codecov.io/gh/boostorg/locale/pull/108?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---
