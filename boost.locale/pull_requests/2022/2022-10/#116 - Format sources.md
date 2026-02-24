# #116 Format sources [Merged]

> Username: Flamefire  
> Created at: 2022-10-03 13:43:31 UTC  
> Updated at: 2022-10-11 08:12:24 UTC  
> Merged at: 2022-10-11 08:12:21 UTC  
> Closed at: 2022-10-11 08:12:21 UTC  
> Url: https://github.com/boostorg/locale/pull/116  

Use clang-format-14 to format source code consistently.  
Closes https://github.com/boostorg/locale/issues/98

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2022-10-04 12:10:13 UTC  
> Updated_at: 2022-10-10 15:23:57 UTC  
> Url: https://github.com/boostorg/locale/pull/116#issuecomment-1266893223  

# [Codecov](https://codecov.io/gh/boostorg/locale/pull/116?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#116](https://codecov.io/gh/boostorg/locale/pull/116?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (907e625) into [develop](https://codecov.io/gh/boostorg/locale/commit/1e0ed6862f5ef8b3b43bc30a122aba981a9bdfe5?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (1e0ed68) will **decrease** coverage by `0.37%`.  
> The diff coverage is `92.03%`.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/locale/pull/116/graphs/tree.svg?width=650&height=150&src=pr&token=vF6mWvgGUn&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://codecov.io/gh/boostorg/locale/pull/116?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #116      +/-   ##  
===========================================  
- Coverage    88.55%   88.18%   -0.38%       
===========================================  
  Files          100      100                
  Lines         9388     8904     -484       
===========================================  
- Hits          8314     7852     -462       
+ Misses        1074     1052      -22       
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/locale/pull/116?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [src/boost/locale/encoding/uconv\_codepage.ipp](https://codecov.io/gh/boostorg/locale/pull/116/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-c3JjL2Jvb3N0L2xvY2FsZS9lbmNvZGluZy91Y29udl9jb2RlcGFnZS5pcHA=) | `0.00% <0.00%> (ø)` | |  
| [src/boost/locale/icu/formatter.cpp](https://codecov.io/gh/boostorg/locale/pull/116/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-c3JjL2Jvb3N0L2xvY2FsZS9pY3UvZm9ybWF0dGVyLmNwcA==) | `82.49% <ø> (-0.49%)` | :arrow_down: |  
| [src/boost/locale/icu/formatter.hpp](https://codecov.io/gh/boostorg/locale/pull/116/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-c3JjL2Jvb3N0L2xvY2FsZS9pY3UvZm9ybWF0dGVyLmhwcA==) | `100.00% <ø> (ø)` | |  
| [src/boost/locale/icu/icu\_backend.cpp](https://codecov.io/gh/boostorg/locale/pull/116/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-c3JjL2Jvb3N0L2xvY2FsZS9pY3UvaWN1X2JhY2tlbmQuY3Bw) | `84.84% <ø> (-1.58%)` | :arrow_down: |  
| [src/boost/locale/icu/icu\_util.hpp](https://codecov.io/gh/boostorg/locale/pull/116/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-c3JjL2Jvb3N0L2xvY2FsZS9pY3UvaWN1X3V0aWwuaHBw) | `63.63% <ø> (ø)` | |  
| [src/boost/locale/icu/numeric.cpp](https://codecov.io/gh/boostorg/locale/pull/116/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-c3JjL2Jvb3N0L2xvY2FsZS9pY3UvbnVtZXJpYy5jcHA=) | `89.56% <ø> (-0.99%)` | :arrow_down: |  
| [src/boost/locale/icu/predefined\_formatters.hpp](https://codecov.io/gh/boostorg/locale/pull/116/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-c3JjL2Jvb3N0L2xvY2FsZS9pY3UvcHJlZGVmaW5lZF9mb3JtYXR0ZXJzLmhwcA==) | `95.16% <ø> (+3.16%)` | :arrow_up: |  
| [src/boost/locale/icu/time\_zone.cpp](https://codecov.io/gh/boostorg/locale/pull/116/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-c3JjL2Jvb3N0L2xvY2FsZS9pY3UvdGltZV96b25lLmNwcA==) | `100.00% <ø> (ø)` | |  
| [src/boost/locale/icu/uconv.hpp](https://codecov.io/gh/boostorg/locale/pull/116/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-c3JjL2Jvb3N0L2xvY2FsZS9pY3UvdWNvbnYuaHBw) | `77.89% <ø> (-2.52%)` | :arrow_down: |  
| [src/boost/locale/posix/codecvt.cpp](https://codecov.io/gh/boostorg/locale/pull/116/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-c3JjL2Jvb3N0L2xvY2FsZS9wb3NpeC9jb2RlY3Z0LmNwcA==) | `88.79% <ø> (-0.38%)` | :arrow_down: |  
| ... and [164 more](https://codecov.io/gh/boostorg/locale/pull/116/diff?src=pr&el=tree-more&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/locale/pull/116?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/locale/pull/116?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [48c7253...907e625](https://codecov.io/gh/boostorg/locale/pull/116?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---
