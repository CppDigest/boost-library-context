# #185 Fix ICU boundary detection [Merged]

> Username: Flamefire  
> Created at: 2023-06-22 21:23:13 UTC  
> Updated at: 2023-06-23 06:44:41 UTC  
> Merged at: 2023-06-23 06:44:38 UTC  
> Closed at: 2023-06-23 06:44:38 UTC  
> Url: https://github.com/boostorg/locale/pull/185  

With the fix for #44 in #154 a bug in older ICU versions (i.e. up to 54.x) is triggered by using the UTF-8 string  in-place via `UText*`  
  
`test_boundary` detects that but such ICU versions are rare due to the age.  
  
- Restrict the optimization to ICU >= 55.2 which has the bug fixed  
- Use RAII for `UText*`  
- Fix test_formatting failures in older ICU versions after #184  
- Fix a possible warning on MSVC caused by ICU headers

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2023-06-23 04:26:47 UTC  
> Url: https://github.com/boostorg/locale/pull/185#issuecomment-1603664598  

## [Codecov](https://app.codecov.io/gh/boostorg/locale/pull/185?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#185](https://app.codecov.io/gh/boostorg/locale/pull/185?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (aa4031a) into [develop](https://app.codecov.io/gh/boostorg/locale/commit/2ada5fe4c5f8862ca9605f87fe9d8d74c9d8c879?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (2ada5fe) will **increase** coverage by `0.04%`.  
> The diff coverage is `97.22%`.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/locale/pull/185/graphs/tree.svg?width=650&height=150&src=pr&token=vF6mWvgGUn&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/locale/pull/185?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #185      +/-   ##  
===========================================  
+ Coverage    92.74%   92.79%   +0.04%       
===========================================  
  Files          112      112                
  Lines         9888     9897       +9       
===========================================  
+ Hits          9171     9184      +13       
+ Misses         717      713       -4       
```  
  
  
| [Impacted Files](https://app.codecov.io/gh/boostorg/locale/pull/185?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [src/boost/locale/icu/formatter.cpp](https://app.codecov.io/gh/boostorg/locale/pull/185?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-c3JjL2Jvb3N0L2xvY2FsZS9pY3UvZm9ybWF0dGVyLmNwcA==) | `78.78% <ø> (ø)` | |  
| [src/boost/locale/util/encoding.hpp](https://app.codecov.io/gh/boostorg/locale/pull/185?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-c3JjL2Jvb3N0L2xvY2FsZS91dGlsL2VuY29kaW5nLmhwcA==) | `100.00% <ø> (ø)` | |  
| [src/boost/locale/icu/boundary.cpp](https://app.codecov.io/gh/boostorg/locale/pull/185?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-c3JjL2Jvb3N0L2xvY2FsZS9pY3UvYm91bmRhcnkuY3Bw) | `93.75% <92.30%> (+3.65%)` | :arrow_up: |  
| [src/boost/locale/util/codecvt\_converter.cpp](https://app.codecov.io/gh/boostorg/locale/pull/185?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-c3JjL2Jvb3N0L2xvY2FsZS91dGlsL2NvZGVjdnRfY29udmVydGVyLmNwcA==) | `94.24% <100.00%> (+0.08%)` | :arrow_up: |  
| [test/test\_encoding.cpp](https://app.codecov.io/gh/boostorg/locale/pull/185?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC90ZXN0X2VuY29kaW5nLmNwcA==) | `97.49% <100.00%> (+0.08%)` | :arrow_up: |  
| [test/test\_formatting.cpp](https://app.codecov.io/gh/boostorg/locale/pull/185?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC90ZXN0X2Zvcm1hdHRpbmcuY3Bw) | `99.74% <100.00%> (ø)` | |  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/locale/pull/185?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/locale/pull/185?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [2ada5fe...aa4031a](https://app.codecov.io/gh/boostorg/locale/pull/185?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---
