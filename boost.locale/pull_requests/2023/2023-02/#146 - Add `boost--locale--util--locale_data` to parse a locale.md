# #146 Add `boost::locale::util::locale_data` to parse a locale [Merged]

> Username: Flamefire  
> Created at: 2023-02-15 19:13:50 UTC  
> Updated at: 2023-02-26 15:57:55 UTC  
> Merged at: 2023-02-26 15:17:41 UTC  
> Closed at: 2023-02-26 15:17:41 UTC  
> Url: https://github.com/boostorg/locale/pull/146  

Move this helper class to the public headers as it might be useful for others.

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2023-02-18 00:28:47 UTC  
> Updated_at: 2023-02-26 11:23:18 UTC  
> Url: https://github.com/boostorg/locale/pull/146#issuecomment-1435421288  

# [Codecov](https://codecov.io/gh/boostorg/locale/pull/146?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#146](https://codecov.io/gh/boostorg/locale/pull/146?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (9c88884) into [develop](https://codecov.io/gh/boostorg/locale/commit/f6d033e968b457d5f9dcc9a5fa0a1f67bfc543d8?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (f6d033e) will **increase** coverage by `0.53%`.  
> The diff coverage is `99.04%`.  
  
> :exclamation: Current head 9c88884 differs from pull request most recent head 3c9a00d. Consider uploading reports for the commit 3c9a00d to get more accurate results  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/locale/pull/146/graphs/tree.svg?width=650&height=150&src=pr&token=vF6mWvgGUn&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://codecov.io/gh/boostorg/locale/pull/146?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #146      +/-   ##  
===========================================  
+ Coverage    88.15%   88.68%   +0.53%       
===========================================  
  Files          106      108       +2       
  Lines         9207     9361     +154       
===========================================  
+ Hits          8116     8302     +186       
+ Misses        1091     1059      -32       
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/locale/pull/146?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [src/boost/locale/encoding/codepage.cpp](https://codecov.io/gh/boostorg/locale/pull/146?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-c3JjL2Jvb3N0L2xvY2FsZS9lbmNvZGluZy9jb2RlcGFnZS5jcHA=) | `73.91% <ø> (-5.40%)` | :arrow_down: |  
| [src/boost/locale/encoding/conv.hpp](https://codecov.io/gh/boostorg/locale/pull/146?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-c3JjL2Jvb3N0L2xvY2FsZS9lbmNvZGluZy9jb252LmhwcA==) | `100.00% <ø> (+7.69%)` | :arrow_up: |  
| [src/boost/locale/util/win\_codepages.hpp](https://codecov.io/gh/boostorg/locale/pull/146?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-c3JjL2Jvb3N0L2xvY2FsZS91dGlsL3dpbl9jb2RlcGFnZXMuaHBw) | `100.00% <ø> (ø)` | |  
| [src/boost/locale/win32/lcid.cpp](https://codecov.io/gh/boostorg/locale/pull/146?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-c3JjL2Jvb3N0L2xvY2FsZS93aW4zMi9sY2lkLmNwcA==) | `86.00% <80.00%> (ø)` | |  
| [src/boost/locale/util/encoding.cpp](https://codecov.io/gh/boostorg/locale/pull/146?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-c3JjL2Jvb3N0L2xvY2FsZS91dGlsL2VuY29kaW5nLmNwcA==) | `92.59% <92.59%> (ø)` | |  
| [include/boost/locale/util/locale\_data.hpp](https://codecov.io/gh/boostorg/locale/pull/146?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9sb2NhbGUvdXRpbC9sb2NhbGVfZGF0YS5ocHA=) | `100.00% <100.00%> (ø)` | |  
| [include/boost/locale/util/string.hpp](https://codecov.io/gh/boostorg/locale/pull/146?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9sb2NhbGUvdXRpbC9zdHJpbmcuaHBw) | `100.00% <100.00%> (ø)` | |  
| [src/boost/locale/encoding/wconv\_codepage.ipp](https://codecov.io/gh/boostorg/locale/pull/146?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-c3JjL2Jvb3N0L2xvY2FsZS9lbmNvZGluZy93Y29udl9jb2RlcGFnZS5pcHA=) | `78.48% <100.00%> (-0.44%)` | :arrow_down: |  
| [src/boost/locale/icu/codecvt.cpp](https://codecov.io/gh/boostorg/locale/pull/146?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-c3JjL2Jvb3N0L2xvY2FsZS9pY3UvY29kZWN2dC5jcHA=) | `77.96% <100.00%> (ø)` | |  
| [src/boost/locale/icu/icu\_backend.cpp](https://codecov.io/gh/boostorg/locale/pull/146?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-c3JjL2Jvb3N0L2xvY2FsZS9pY3UvaWN1X2JhY2tlbmQuY3Bw) | `82.35% <100.00%> (ø)` | |  
| ... and [15 more](https://codecov.io/gh/boostorg/locale/pull/146?src=pr&el=tree-more&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/locale/pull/146?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/locale/pull/146?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [f6d033e...3c9a00d](https://codecov.io/gh/boostorg/locale/pull/146?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---
