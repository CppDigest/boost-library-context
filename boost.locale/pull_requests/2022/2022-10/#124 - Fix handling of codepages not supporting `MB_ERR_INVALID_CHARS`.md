# #124 Fix handling of codepages not supporting `MB_ERR_INVALID_CHARS` [Merged]

> Username: Flamefire  
> Created at: 2022-10-22 12:14:23 UTC  
> Updated at: 2022-10-23 12:09:21 UTC  
> Merged at: 2022-10-23 12:09:18 UTC  
> Closed at: 2022-10-23 12:09:18 UTC  
> Url: https://github.com/boostorg/locale/pull/124  

E.g. iso-2022-jp requires `flags=0` to be passed to `MultiByteToWideChar`  
according to https://learn.microsoft.com/en-us/windows/win32/api/stringapiset/nf-stringapiset-multibytetowidechar  
So catch the `ERROR_INVALID_FLAGS` and reset the flags to zero.  
  
Fixes #121  
  
Also some refactoring to reduce code duplication and improve readability of tests

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2022-10-22 21:03:11 UTC  
> Url: https://github.com/boostorg/locale/pull/124#issuecomment-1287918857  

# [Codecov](https://codecov.io/gh/boostorg/locale/pull/124?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#124](https://codecov.io/gh/boostorg/locale/pull/124?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (5280046) into [develop](https://codecov.io/gh/boostorg/locale/commit/cc0337c6a93d2637d8157269a59912f68f8db3b4?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (cc0337c) will **decrease** coverage by `0.08%`.  
> The diff coverage is `95.94%`.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/locale/pull/124/graphs/tree.svg?width=650&height=150&src=pr&token=vF6mWvgGUn&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://codecov.io/gh/boostorg/locale/pull/124?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #124      +/-   ##  
===========================================  
- Coverage    88.45%   88.37%   -0.09%       
===========================================  
  Files          100      101       +1       
  Lines         8977     8921      -56       
===========================================  
- Hits          7941     7884      -57       
- Misses        1036     1037       +1       
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/locale/pull/124?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/locale/message.hpp](https://codecov.io/gh/boostorg/locale/pull/124/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9sb2NhbGUvbWVzc2FnZS5ocHA=) | `92.61% <ø> (ø)` | |  
| [src/boost/locale/icu/uconv.hpp](https://codecov.io/gh/boostorg/locale/pull/124/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-c3JjL2Jvb3N0L2xvY2FsZS9pY3UvdWNvbnYuaHBw) | `77.89% <ø> (ø)` | |  
| [src/boost/locale/encoding/wconv\_codepage.ipp](https://codecov.io/gh/boostorg/locale/pull/124/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-c3JjL2Jvb3N0L2xvY2FsZS9lbmNvZGluZy93Y29udl9jb2RlcGFnZS5pcHA=) | `79.14% <81.81%> (-0.19%)` | :arrow_down: |  
| [test/test\_codepage.cpp](https://codecov.io/gh/boostorg/locale/pull/124/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC90ZXN0X2NvZGVwYWdlLmNwcA==) | `95.05% <93.33%> (+0.40%)` | :arrow_up: |  
| [include/boost/locale/boundary/segment.hpp](https://codecov.io/gh/boostorg/locale/pull/124/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9sb2NhbGUvYm91bmRhcnkvc2VnbWVudC5ocHA=) | `100.00% <100.00%> (ø)` | |  
| [include/boost/locale/conversion.hpp](https://codecov.io/gh/boostorg/locale/pull/124/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9sb2NhbGUvY29udmVyc2lvbi5ocHA=) | `100.00% <100.00%> (ø)` | |  
| [include/boost/locale/encoding.hpp](https://codecov.io/gh/boostorg/locale/pull/124/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9sb2NhbGUvZW5jb2RpbmcuaHBw) | `100.00% <100.00%> (ø)` | |  
| [include/boost/locale/encoding\_utf.hpp](https://codecov.io/gh/boostorg/locale/pull/124/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9sb2NhbGUvZW5jb2RpbmdfdXRmLmhwcA==) | `100.00% <100.00%> (ø)` | |  
| [include/boost/locale/formatting.hpp](https://codecov.io/gh/boostorg/locale/pull/124/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9sb2NhbGUvZm9ybWF0dGluZy5ocHA=) | `99.13% <100.00%> (-0.02%)` | :arrow_down: |  
| [include/boost/locale/util/string.hpp](https://codecov.io/gh/boostorg/locale/pull/124/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9sb2NhbGUvdXRpbC9zdHJpbmcuaHBw) | `100.00% <100.00%> (ø)` | |  
| ... and [6 more](https://codecov.io/gh/boostorg/locale/pull/124/diff?src=pr&el=tree-more&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/locale/pull/124?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/locale/pull/124?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [cc0337c...5280046](https://codecov.io/gh/boostorg/locale/pull/124?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---
