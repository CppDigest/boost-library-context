# #177 Ensure that UTF-8 encoding is used when requested in std backend [Merged]

> Username: Flamefire  
> Created at: 2023-06-08 17:39:14 UTC  
> Updated at: 2023-06-10 07:51:16 UTC  
> Merged at: 2023-06-10 07:51:14 UTC  
> Closed at: 2023-06-10 07:51:14 UTC  
> Url: https://github.com/boostorg/locale/pull/177  

When a locale doesn't exist on the system don't simply fall back to the classic locale but try to preserve the UTF-8 encoding if requested.  
  
For the std backend this means trying e.g. `"C.UTF-8"` and using `utf8_support::from_wide` as a best-effort  
  
Related to #172 especially as the `to_windows_name` function can now report failure.

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2023-06-08 22:43:28 UTC  
> Updated_at: 2023-06-09 18:38:02 UTC  
> Url: https://github.com/boostorg/locale/pull/177#issuecomment-1583528713  

## [Codecov](https://app.codecov.io/gh/boostorg/locale/pull/177?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#177](https://app.codecov.io/gh/boostorg/locale/pull/177?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (c594880) into [develop](https://app.codecov.io/gh/boostorg/locale/commit/a821f487fb56645ce378b988a1892abf5de261bd?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (a821f48) will **increase** coverage by `0.11%`.  
> The diff coverage is `98.18%`.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/locale/pull/177/graphs/tree.svg?width=650&height=150&src=pr&token=vF6mWvgGUn&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/locale/pull/177?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #177      +/-   ##  
===========================================  
+ Coverage    91.66%   91.78%   +0.11%       
===========================================  
  Files          112      112                
  Lines         9795     9832      +37       
===========================================  
+ Hits          8979     9024      +45       
+ Misses         816      808       -8       
```  
  
  
| [Impacted Files](https://app.codecov.io/gh/boostorg/locale/pull/177?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/locale/util/locale\_data.hpp](https://app.codecov.io/gh/boostorg/locale/pull/177?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9sb2NhbGUvdXRpbC9sb2NhbGVfZGF0YS5ocHA=) | `100.00% <ø> (ø)` | |  
| [src/boost/locale/util/encoding.hpp](https://app.codecov.io/gh/boostorg/locale/pull/177?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-c3JjL2Jvb3N0L2xvY2FsZS91dGlsL2VuY29kaW5nLmhwcA==) | `100.00% <ø> (ø)` | |  
| [src/boost/locale/std/std\_backend.cpp](https://app.codecov.io/gh/boostorg/locale/pull/177?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-c3JjL2Jvb3N0L2xvY2FsZS9zdGQvc3RkX2JhY2tlbmQuY3Bw) | `88.23% <95.00%> (+2.65%)` | :arrow_up: |  
| [src/boost/locale/util/encoding.cpp](https://app.codecov.io/gh/boostorg/locale/pull/177?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-c3JjL2Jvb3N0L2xvY2FsZS91dGlsL2VuY29kaW5nLmNwcA==) | `95.00% <100.00%> (+3.00%)` | :arrow_up: |  
| [src/boost/locale/util/locale\_data.cpp](https://app.codecov.io/gh/boostorg/locale/pull/177?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-c3JjL2Jvb3N0L2xvY2FsZS91dGlsL2xvY2FsZV9kYXRhLmNwcA==) | `100.00% <100.00%> (ø)` | |  
| [test/test\_generator.cpp](https://app.codecov.io/gh/boostorg/locale/pull/177?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC90ZXN0X2dlbmVyYXRvci5jcHA=) | `100.00% <100.00%> (ø)` | |  
  
... and [2 files with indirect coverage changes](https://app.codecov.io/gh/boostorg/locale/pull/177/indirect-changes?src=pr&el=tree-more&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/locale/pull/177?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/locale/pull/177?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [a821f48...c594880](https://app.codecov.io/gh/boostorg/locale/pull/177?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---
