# #168 Modernize using nullptr [Merged]

> Username: Flamefire  
> Created at: 2023-05-06 12:39:15 UTC  
> Updated at: 2023-05-07 17:18:35 UTC  
> Merged at: 2023-05-07 17:01:45 UTC  
> Closed at: 2023-05-07 17:01:45 UTC  
> Url: https://github.com/boostorg/locale/pull/168  

Allow to tell places where the integer zero is used apart from pointer usages

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2023-05-06 16:33:42 UTC  
> Updated_at: 2023-05-07 10:09:27 UTC  
> Url: https://github.com/boostorg/locale/pull/168#issuecomment-1537177310  

## [Codecov](https://app.codecov.io/gh/boostorg/locale/pull/168?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#168](https://app.codecov.io/gh/boostorg/locale/pull/168?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (0ecd247) into [develop](https://app.codecov.io/gh/boostorg/locale/commit/a5bd883134208ca209893f58f3bb2b9f0c2abb96?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (a5bd883) will **decrease** coverage by `0.01%`.  
> The diff coverage is `92.50%`.  
  
> :exclamation: Current head 0ecd247 differs from pull request most recent head 2018b24. Consider uploading reports for the commit 2018b24 to get more accurate results  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/locale/pull/168/graphs/tree.svg?width=650&height=150&src=pr&token=vF6mWvgGUn&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/locale/pull/168?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #168      +/-   ##  
===========================================  
- Coverage    90.74%   90.74%   -0.01%       
===========================================  
  Files          111      111                
  Lines         9642     9638       -4       
===========================================  
- Hits          8750     8746       -4       
  Misses         892      892                
```  
  
  
| [Impacted Files](https://app.codecov.io/gh/boostorg/locale/pull/168?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/locale/date\_time.hpp](https://app.codecov.io/gh/boostorg/locale/pull/168?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9sb2NhbGUvZGF0ZV90aW1lLmhwcA==) | `93.98% <ø> (ø)` | |  
| [src/boost/locale/icu/time\_zone.cpp](https://app.codecov.io/gh/boostorg/locale/pull/168?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-c3JjL2Jvb3N0L2xvY2FsZS9pY3UvdGltZV96b25lLmNwcA==) | `100.00% <ø> (ø)` | |  
| [src/boost/locale/util/codecvt\_converter.cpp](https://app.codecov.io/gh/boostorg/locale/pull/168?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-c3JjL2Jvb3N0L2xvY2FsZS91dGlsL2NvZGVjdnRfY29udmVydGVyLmNwcA==) | `88.48% <0.00%> (ø)` | |  
| [test/test\_posix\_convert.cpp](https://app.codecov.io/gh/boostorg/locale/pull/168?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC90ZXN0X3Bvc2l4X2NvbnZlcnQuY3Bw) | `81.57% <0.00%> (ø)` | |  
| [src/boost/locale/encoding/wconv\_codepage.ipp](https://app.codecov.io/gh/boostorg/locale/pull/168?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-c3JjL2Jvb3N0L2xvY2FsZS9lbmNvZGluZy93Y29udl9jb2RlcGFnZS5pcHA=) | `78.10% <50.00%> (-0.39%)` | :arrow_down: |  
| [src/boost/locale/util/gregorian.cpp](https://app.codecov.io/gh/boostorg/locale/pull/168?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-c3JjL2Jvb3N0L2xvY2FsZS91dGlsL2dyZWdvcmlhbi5jcHA=) | `71.15% <75.00%> (-0.07%)` | :arrow_down: |  
| [include/boost/locale/boundary/index.hpp](https://app.codecov.io/gh/boostorg/locale/pull/168?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9sb2NhbGUvYm91bmRhcnkvaW5kZXguaHBw) | `99.17% <100.00%> (ø)` | |  
| [include/boost/locale/format.hpp](https://app.codecov.io/gh/boostorg/locale/pull/168?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9sb2NhbGUvZm9ybWF0LmhwcA==) | `94.32% <100.00%> (ø)` | |  
| [include/boost/locale/formatting.hpp](https://app.codecov.io/gh/boostorg/locale/pull/168?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9sb2NhbGUvZm9ybWF0dGluZy5ocHA=) | `100.00% <100.00%> (ø)` | |  
| [include/boost/locale/message.hpp](https://app.codecov.io/gh/boostorg/locale/pull/168?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9sb2NhbGUvbWVzc2FnZS5ocHA=) | `100.00% <100.00%> (ø)` | |  
| ... and [19 more](https://app.codecov.io/gh/boostorg/locale/pull/168?src=pr&el=tree-more&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | |  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/locale/pull/168?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/locale/pull/168?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [a5bd883...2018b24](https://app.codecov.io/gh/boostorg/locale/pull/168?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---
