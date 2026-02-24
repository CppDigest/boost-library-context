# #179 Improve tests [Merged]

> Username: Flamefire  
> Created at: 2023-06-13 09:36:30 UTC  
> Updated at: 2023-06-14 07:30:36 UTC  
> Merged at: 2023-06-14 07:30:33 UTC  
> Closed at: 2023-06-14 07:30:33 UTC  
> Url: https://github.com/boostorg/locale/pull/179  

Add test for the char-type argument of `backend::install`: Make sure it is used when it should and ignored when it shouldn't.  
  
Add a test for `use_ansi_encoding` (might not set/reset correctly)  
  
Cleanup: Remove `localization_backend_manager::get` and unimplemented generator methods.

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2023-06-13 21:08:25 UTC  
> Updated_at: 2023-06-13 21:53:22 UTC  
> Url: https://github.com/boostorg/locale/pull/179#issuecomment-1590029722  

## [Codecov](https://app.codecov.io/gh/boostorg/locale/pull/179?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#179](https://app.codecov.io/gh/boostorg/locale/pull/179?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (30987bb) into [develop](https://app.codecov.io/gh/boostorg/locale/commit/458aeee83c41e099b8e5e2ef40afcaa5542cd724?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (458aeee) will **increase** coverage by `0.67%`.  
> The diff coverage is `97.14%`.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/locale/pull/179/graphs/tree.svg?width=650&height=150&src=pr&token=vF6mWvgGUn&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/locale/pull/179?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #179      +/-   ##  
===========================================  
+ Coverage    91.67%   92.35%   +0.67%       
===========================================  
  Files          112      112                
  Lines         9801     9859      +58       
===========================================  
+ Hits          8985     9105     +120       
+ Misses         816      754      -62       
```  
  
  
| [Impacted Files](https://app.codecov.io/gh/boostorg/locale/pull/179?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/locale/generator.hpp](https://app.codecov.io/gh/boostorg/locale/pull/179?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9sb2NhbGUvZ2VuZXJhdG9yLmhwcA==) | `100.00% <ø> (ø)` | |  
| [include/boost/locale/localization\_backend.hpp](https://app.codecov.io/gh/boostorg/locale/pull/179?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9sb2NhbGUvbG9jYWxpemF0aW9uX2JhY2tlbmQuaHBw) | `42.85% <0.00%> (-7.15%)` | :arrow_down: |  
| [src/boost/locale/shared/generator.cpp](https://app.codecov.io/gh/boostorg/locale/pull/179?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-c3JjL2Jvb3N0L2xvY2FsZS9zaGFyZWQvZ2VuZXJhdG9yLmNwcA==) | `86.04% <100.00%> (+4.43%)` | :arrow_up: |  
| [src/boost/locale/shared/localization\_backend.cpp](https://app.codecov.io/gh/boostorg/locale/pull/179?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-c3JjL2Jvb3N0L2xvY2FsZS9zaGFyZWQvbG9jYWxpemF0aW9uX2JhY2tlbmQuY3Bw) | `79.48% <100.00%> (+1.33%)` | :arrow_up: |  
| [src/boost/locale/win32/win\_backend.cpp](https://app.codecov.io/gh/boostorg/locale/pull/179?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-c3JjL2Jvb3N0L2xvY2FsZS93aW4zMi93aW5fYmFja2VuZC5jcHA=) | `90.56% <100.00%> (+1.67%)` | :arrow_up: |  
| [test/test\_generator.cpp](https://app.codecov.io/gh/boostorg/locale/pull/179?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC90ZXN0X2dlbmVyYXRvci5jcHA=) | `100.00% <100.00%> (ø)` | |  
  
... and [23 files with indirect coverage changes](https://app.codecov.io/gh/boostorg/locale/pull/179/indirect-changes?src=pr&el=tree-more&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/locale/pull/179?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/locale/pull/179?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [458aeee...30987bb](https://app.codecov.io/gh/boostorg/locale/pull/179?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---
