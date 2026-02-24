# #128 Use enum classes for facet types [Merged]

> Username: Flamefire  
> Created at: 2022-10-23 12:06:37 UTC  
> Updated at: 2022-10-25 07:19:09 UTC  
> Merged at: 2022-10-25 07:19:06 UTC  
> Closed at: 2022-10-25 07:19:06 UTC  
> Url: https://github.com/boostorg/locale/pull/128  

Changes `character_facet_type` & `locale_category_type` to the enum classes `char_facet_t` & `category_t` This avoids a name clash with e.g. `calendar_facet` but is a breaking change

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2022-10-24 22:53:54 UTC  
> Updated_at: 2022-10-24 23:11:08 UTC  
> Url: https://github.com/boostorg/locale/pull/128#issuecomment-1289733045  

# [Codecov](https://codecov.io/gh/boostorg/locale/pull/128?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#128](https://codecov.io/gh/boostorg/locale/pull/128?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (b70e3a6) into [develop](https://codecov.io/gh/boostorg/locale/commit/64ad69ce421a490bd314bf4b2a4fc15b628ed816?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (64ad69c) will **decrease** coverage by `0.57%`.  
> The diff coverage is `75.22%`.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/locale/pull/128/graphs/tree.svg?width=650&height=150&src=pr&token=vF6mWvgGUn&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://codecov.io/gh/boostorg/locale/pull/128?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #128      +/-   ##  
===========================================  
- Coverage    88.35%   87.77%   -0.58%       
===========================================  
  Files          102      102                
  Lines         8940     9016      +76       
===========================================  
+ Hits          7899     7914      +15       
- Misses        1041     1102      +61       
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/locale/pull/128?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/locale/localization\_backend.hpp](https://codecov.io/gh/boostorg/locale/pull/128/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9sb2NhbGUvbG9jYWxpemF0aW9uX2JhY2tlbmQuaHBw) | `75.00% <ø> (ø)` | |  
| [include/boost/locale/util.hpp](https://codecov.io/gh/boostorg/locale/pull/128/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9sb2NhbGUvdXRpbC5ocHA=) | `10.00% <ø> (ø)` | |  
| [src/boost/locale/icu/date\_time.cpp](https://codecov.io/gh/boostorg/locale/pull/128/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-c3JjL2Jvb3N0L2xvY2FsZS9pY3UvZGF0ZV90aW1lLmNwcA==) | `83.07% <0.00%> (-0.65%)` | :arrow_down: |  
| [src/boost/locale/icu/formatter.cpp](https://codecov.io/gh/boostorg/locale/pull/128/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-c3JjL2Jvb3N0L2xvY2FsZS9pY3UvZm9ybWF0dGVyLmNwcA==) | `82.55% <ø> (ø)` | |  
| [src/boost/locale/util/gregorian.cpp](https://codecov.io/gh/boostorg/locale/pull/128/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-c3JjL2Jvb3N0L2xvY2FsZS91dGlsL2dyZWdvcmlhbi5jcHA=) | `71.22% <0.00%> (-0.82%)` | :arrow_down: |  
| [src/boost/locale/util/info.cpp](https://codecov.io/gh/boostorg/locale/pull/128/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-c3JjL2Jvb3N0L2xvY2FsZS91dGlsL2luZm8uY3Bw) | `86.66% <0.00%> (ø)` | |  
| [src/boost/locale/posix/converter.cpp](https://codecov.io/gh/boostorg/locale/pull/128/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-c3JjL2Jvb3N0L2xvY2FsZS9wb3NpeC9jb252ZXJ0ZXIuY3Bw) | `85.45% <27.27%> (-10.55%)` | :arrow_down: |  
| [src/boost/locale/std/converter.cpp](https://codecov.io/gh/boostorg/locale/pull/128/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-c3JjL2Jvb3N0L2xvY2FsZS9zdGQvY29udmVydGVyLmNwcA==) | `82.60% <27.27%> (-12.52%)` | :arrow_down: |  
| [src/boost/locale/win32/converter.cpp](https://codecov.io/gh/boostorg/locale/pull/128/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-c3JjL2Jvb3N0L2xvY2FsZS93aW4zMi9jb252ZXJ0ZXIuY3Bw) | `86.95% <40.00%> (-4.35%)` | :arrow_down: |  
| [src/boost/locale/win32/numeric.cpp](https://codecov.io/gh/boostorg/locale/pull/128/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-c3JjL2Jvb3N0L2xvY2FsZS93aW4zMi9udW1lcmljLmNwcA==) | `73.13% <50.00%> (-5.33%)` | :arrow_down: |  
| ... and [31 more](https://codecov.io/gh/boostorg/locale/pull/128/diff?src=pr&el=tree-more&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/locale/pull/128?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/locale/pull/128?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [64ad69c...b70e3a6](https://codecov.io/gh/boostorg/locale/pull/128?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---
