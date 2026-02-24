# #122 Fix FreeBSD support [Merged]

> Username: Flamefire  
> Created at: 2022-10-21 19:30:50 UTC  
> Updated at: 2022-10-22 11:34:51 UTC  
> Merged at: 2022-10-22 11:34:47 UTC  
> Closed at: 2022-10-22 11:34:47 UTC  
> Url: https://github.com/boostorg/locale/pull/122  

Mostly a fix for a warning and a workaround for the CMake build at least.

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2022-10-21 22:00:44 UTC  
> Updated_at: 2022-10-22 11:28:45 UTC  
> Url: https://github.com/boostorg/locale/pull/122#issuecomment-1287477782  

# [Codecov](https://codecov.io/gh/boostorg/locale/pull/122?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#122](https://codecov.io/gh/boostorg/locale/pull/122?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (5641825) into [develop](https://codecov.io/gh/boostorg/locale/commit/7a9c4cfc344b5c6303568863416986e56eb610d4?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (7a9c4cf) will **decrease** coverage by `0.00%`.  
> The diff coverage is `100.00%`.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/locale/pull/122/graphs/tree.svg?width=650&height=150&src=pr&token=vF6mWvgGUn&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://codecov.io/gh/boostorg/locale/pull/122?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #122      +/-   ##  
===========================================  
- Coverage    88.46%   88.45%   -0.01%       
===========================================  
  Files          100      100                
  Lines         8982     8977       -5       
===========================================  
- Hits          7946     7941       -5       
  Misses        1036     1036                
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/locale/pull/122?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/locale/boundary/index.hpp](https://codecov.io/gh/boostorg/locale/pull/122/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9sb2NhbGUvYm91bmRhcnkvaW5kZXguaHBw) | `98.76% <ø> (ø)` | |  
| [src/boost/locale/icu/date\_time.cpp](https://codecov.io/gh/boostorg/locale/pull/122/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-c3JjL2Jvb3N0L2xvY2FsZS9pY3UvZGF0ZV90aW1lLmNwcA==) | `83.72% <ø> (ø)` | |  
| [src/boost/locale/icu/formatter.cpp](https://codecov.io/gh/boostorg/locale/pull/122/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-c3JjL2Jvb3N0L2xvY2FsZS9pY3UvZm9ybWF0dGVyLmNwcA==) | `82.88% <ø> (ø)` | |  
| [src/boost/locale/util/codecvt\_converter.cpp](https://codecov.io/gh/boostorg/locale/pull/122/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-c3JjL2Jvb3N0L2xvY2FsZS91dGlsL2NvZGVjdnRfY29udmVydGVyLmNwcA==) | `92.64% <ø> (ø)` | |  
| [src/boost/locale/encoding/iconv\_codepage.ipp](https://codecov.io/gh/boostorg/locale/pull/122/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-c3JjL2Jvb3N0L2xvY2FsZS9lbmNvZGluZy9pY29udl9jb2RlcGFnZS5pcHA=) | `90.90% <100.00%> (-0.14%)` | :arrow_down: |  
| [src/boost/locale/posix/codecvt.cpp](https://codecov.io/gh/boostorg/locale/pull/122/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-c3JjL2Jvb3N0L2xvY2FsZS9wb3NpeC9jb2RlY3Z0LmNwcA==) | `87.96% <100.00%> (-0.84%)` | :arrow_down: |  
| [src/boost/locale/util/iconv.hpp](https://codecov.io/gh/boostorg/locale/pull/122/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-c3JjL2Jvb3N0L2xvY2FsZS91dGlsL2ljb252LmhwcA==) | `100.00% <100.00%> (ø)` | |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/locale/pull/122?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/locale/pull/122?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [7a9c4cf...5641825](https://codecov.io/gh/boostorg/locale/pull/122?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---
