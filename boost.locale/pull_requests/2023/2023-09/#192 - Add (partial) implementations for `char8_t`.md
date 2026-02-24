# #192 Add (partial) implementations for `char8_t` [Merged]

> Username: Flamefire  
> Created at: 2023-09-10 18:41:52 UTC  
> Updated at: 2023-09-14 14:13:59 UTC  
> Merged at: 2023-09-14 14:13:55 UTC  
> Closed at: 2023-09-14 14:13:55 UTC  
> Url: https://github.com/boostorg/locale/pull/192  

This allows usages like  
```  
std::string u8ToGbk(const std::u8string &U8Str) {  
    return boost::locale::conv::from_utf(U8Str, "GBK");      
}  
```  
as found in #190   
See also #79

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2023-09-11 17:42:03 UTC  
> Updated_at: 2023-09-14 13:18:29 UTC  
> Url: https://github.com/boostorg/locale/pull/192#issuecomment-1714320008  

## [Codecov](https://app.codecov.io/gh/boostorg/locale/pull/192?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#192](https://app.codecov.io/gh/boostorg/locale/pull/192?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (e6b02a4) into [develop](https://app.codecov.io/gh/boostorg/locale/commit/0552ffc29ff11e4fe130b7143ea6ac2bee7b15c6?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (0552ffc) will **decrease** coverage by `0.10%`.  
> The diff coverage is `90.55%`.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/locale/pull/192/graphs/tree.svg?width=650&height=150&src=pr&token=vF6mWvgGUn&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/locale/pull/192?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #192      +/-   ##  
===========================================  
- Coverage    94.69%   94.60%   -0.10%       
===========================================  
  Files          113      114       +1       
  Lines         9857     9857                
===========================================  
- Hits          9334     9325       -9       
- Misses         523      532       +9       
```  
  
  
| [Files Changed](https://app.codecov.io/gh/boostorg/locale/pull/192?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/locale/boundary/boundary\_point.hpp](https://app.codecov.io/gh/boostorg/locale/pull/192?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9sb2NhbGUvYm91bmRhcnkvYm91bmRhcnlfcG9pbnQuaHBw) | `100.00% <ø> (ø)` | |  
| [include/boost/locale/boundary/index.hpp](https://app.codecov.io/gh/boostorg/locale/pull/192?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9sb2NhbGUvYm91bmRhcnkvaW5kZXguaHBw) | `99.17% <ø> (ø)` | |  
| [include/boost/locale/boundary/segment.hpp](https://app.codecov.io/gh/boostorg/locale/pull/192?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9sb2NhbGUvYm91bmRhcnkvc2VnbWVudC5ocHA=) | `100.00% <ø> (ø)` | |  
| [include/boost/locale/format.hpp](https://app.codecov.io/gh/boostorg/locale/pull/192?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9sb2NhbGUvZm9ybWF0LmhwcA==) | `97.81% <ø> (ø)` | |  
| [include/boost/locale/generator.hpp](https://app.codecov.io/gh/boostorg/locale/pull/192?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9sb2NhbGUvZ2VuZXJhdG9yLmhwcA==) | `100.00% <ø> (ø)` | |  
| [include/boost/locale/generic\_codecvt.hpp](https://app.codecov.io/gh/boostorg/locale/pull/192?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9sb2NhbGUvZ2VuZXJpY19jb2RlY3Z0LmhwcA==) | `96.36% <ø> (ø)` | |  
| [include/boost/locale/message.hpp](https://app.codecov.io/gh/boostorg/locale/pull/192?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9sb2NhbGUvbWVzc2FnZS5ocHA=) | `100.00% <ø> (ø)` | |  
| [include/boost/locale/utf8\_codecvt.hpp](https://app.codecov.io/gh/boostorg/locale/pull/192?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9sb2NhbGUvdXRmOF9jb2RlY3Z0LmhwcA==) | `100.00% <ø> (ø)` | |  
| [src/boost/locale/encoding/codepage.cpp](https://app.codecov.io/gh/boostorg/locale/pull/192?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-c3JjL2Jvb3N0L2xvY2FsZS9lbmNvZGluZy9jb2RlcGFnZS5jcHA=) | `97.40% <ø> (ø)` | |  
| [src/boost/locale/icu/formatter.cpp](https://app.codecov.io/gh/boostorg/locale/pull/192?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-c3JjL2Jvb3N0L2xvY2FsZS9pY3UvZm9ybWF0dGVyLmNwcA==) | `78.78% <ø> (ø)` | |  
| ... and [33 more](https://app.codecov.io/gh/boostorg/locale/pull/192?src=pr&el=tree-more&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | |  
  
... and [2 files with indirect coverage changes](https://app.codecov.io/gh/boostorg/locale/pull/192/indirect-changes?src=pr&el=tree-more&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/locale/pull/192?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/locale/pull/192?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [0552ffc...e6b02a4](https://app.codecov.io/gh/boostorg/locale/pull/192?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---
