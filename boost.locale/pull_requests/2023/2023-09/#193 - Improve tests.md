# #193 Improve tests [Merged]

> Username: Flamefire  
> Created at: 2023-09-15 19:01:20 UTC  
> Updated at: 2023-09-17 08:39:18 UTC  
> Merged at: 2023-09-17 08:39:15 UTC  
> Closed at: 2023-09-17 08:39:15 UTC  
> Url: https://github.com/boostorg/locale/pull/193  

Coverage guided test improvements

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2023-09-17 00:48:22 UTC  
> Url: https://github.com/boostorg/locale/pull/193#issuecomment-1722355614  

## [Codecov](https://app.codecov.io/gh/boostorg/locale/pull/193?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#193](https://app.codecov.io/gh/boostorg/locale/pull/193?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (e6e3b33) into [develop](https://app.codecov.io/gh/boostorg/locale/commit/fc2ccba1d47b5a3f374b7a7b57a01ac1999a5f2a?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (fc2ccba) will **increase** coverage by `0.22%`.  
> The diff coverage is `100.00%`.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/locale/pull/193/graphs/tree.svg?width=650&height=150&src=pr&token=vF6mWvgGUn&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/locale/pull/193?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #193      +/-   ##  
===========================================  
+ Coverage    94.60%   94.82%   +0.22%       
===========================================  
  Files          114      114                
  Lines         9857     9878      +21       
===========================================  
+ Hits          9325     9367      +42       
+ Misses         532      511      -21       
```  
  
  
| [Files Changed](https://app.codecov.io/gh/boostorg/locale/pull/193?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/locale/info.hpp](https://app.codecov.io/gh/boostorg/locale/pull/193?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9sb2NhbGUvaW5mby5ocHA=) | `100.00% <ø> (ø)` | |  
| [src/boost/locale/icu/conversion.cpp](https://app.codecov.io/gh/boostorg/locale/pull/193?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-c3JjL2Jvb3N0L2xvY2FsZS9pY3UvY29udmVyc2lvbi5jcHA=) | `92.75% <ø> (+5.08%)` | :arrow_up: |  
| [src/boost/locale/util/info.cpp](https://app.codecov.io/gh/boostorg/locale/pull/193?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-c3JjL2Jvb3N0L2xvY2FsZS91dGlsL2luZm8uY3Bw) | `100.00% <100.00%> (+20.00%)` | :arrow_up: |  
| [src/boost/locale/win32/converter.cpp](https://app.codecov.io/gh/boostorg/locale/pull/193?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-c3JjL2Jvb3N0L2xvY2FsZS93aW4zMi9jb252ZXJ0ZXIuY3Bw) | `93.75% <100.00%> (+2.08%)` | :arrow_up: |  
| [test/case\_convert\_test.hpp](https://app.codecov.io/gh/boostorg/locale/pull/193?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC9jYXNlX2NvbnZlcnRfdGVzdC5ocHA=) | `100.00% <100.00%> (ø)` | |  
| [test/show\_config.cpp](https://app.codecov.io/gh/boostorg/locale/pull/193?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC9zaG93X2NvbmZpZy5jcHA=) | `100.00% <100.00%> (ø)` | |  
| [test/test\_generator.cpp](https://app.codecov.io/gh/boostorg/locale/pull/193?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC90ZXN0X2dlbmVyYXRvci5jcHA=) | `99.55% <100.00%> (+0.02%)` | :arrow_up: |  
| [test/test\_posix\_convert.cpp](https://app.codecov.io/gh/boostorg/locale/pull/193?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC90ZXN0X3Bvc2l4X2NvbnZlcnQuY3Bw) | `100.00% <100.00%> (ø)` | |  
| [test/test\_std\_convert.cpp](https://app.codecov.io/gh/boostorg/locale/pull/193?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC90ZXN0X3N0ZF9jb252ZXJ0LmNwcA==) | `100.00% <100.00%> (ø)` | |  
| [test/test\_winapi\_convert.cpp](https://app.codecov.io/gh/boostorg/locale/pull/193?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC90ZXN0X3dpbmFwaV9jb252ZXJ0LmNwcA==) | `100.00% <100.00%> (ø)` | |  
  
... and [3 files with indirect coverage changes](https://app.codecov.io/gh/boostorg/locale/pull/193/indirect-changes?src=pr&el=tree-more&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/locale/pull/193?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/locale/pull/193?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [fc2ccba...e6e3b33](https://app.codecov.io/gh/boostorg/locale/pull/193?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---
