# #184 Fix icu versions tests [Merged]

> Username: Flamefire  
> Created at: 2023-06-20 17:35:32 UTC  
> Updated at: 2023-06-21 07:45:49 UTC  
> Merged at: 2023-06-21 07:45:46 UTC  
> Closed at: 2023-06-21 07:45:46 UTC  
> Url: https://github.com/boostorg/locale/pull/184  

ICU changed the time format again (in the CLDR) using a NBSP before AM/PM instead of a real space.  
Hence query ICU directly for the expected result.  
But also use a hard-coded value for parsing tests to ensure backwards compatibility.  
  
Fixes #176

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2023-06-21 02:05:45 UTC  
> Updated_at: 2023-06-21 04:16:48 UTC  
> Url: https://github.com/boostorg/locale/pull/184#issuecomment-1599961034  

## [Codecov](https://app.codecov.io/gh/boostorg/locale/pull/184?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#184](https://app.codecov.io/gh/boostorg/locale/pull/184?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (219c834) into [develop](https://app.codecov.io/gh/boostorg/locale/commit/e910a195e3fc98e2241ee9a2a4fac88c0f244a33?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (e910a19) will **increase** coverage by `0.05%`.  
> The diff coverage is `100.00%`.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/locale/pull/184/graphs/tree.svg?width=650&height=150&src=pr&token=vF6mWvgGUn&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/locale/pull/184?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #184      +/-   ##  
===========================================  
+ Coverage    92.69%   92.74%   +0.05%       
===========================================  
  Files          112      112                
  Lines         9820     9888      +68       
===========================================  
+ Hits          9103     9171      +68       
  Misses         717      717                
```  
  
  
| [Impacted Files](https://app.codecov.io/gh/boostorg/locale/pull/184?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [test/test\_formatting.cpp](https://app.codecov.io/gh/boostorg/locale/pull/184?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC90ZXN0X2Zvcm1hdHRpbmcuY3Bw) | `99.74% <100.00%> (+0.05%)` | :arrow_up: |  
  
... and [8 files with indirect coverage changes](https://app.codecov.io/gh/boostorg/locale/pull/184/indirect-changes?src=pr&el=tree-more&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/locale/pull/184?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/locale/pull/184?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [e910a19...219c834](https://app.codecov.io/gh/boostorg/locale/pull/184?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---
