# #175 Make std backend fall back to classic locale instead of system locale [Merged]

> Username: Flamefire  
> Created at: 2023-05-24 15:01:20 UTC  
> Updated at: 2023-05-25 10:44:08 UTC  
> Merged at: 2023-05-25 10:44:05 UTC  
> Closed at: 2023-05-25 10:44:05 UTC  
> Url: https://github.com/boostorg/locale/pull/175  

Partial fix of https://github.com/boostorg/locale/issues/172  
  
Also enable the same fallback-handling for Cygwin

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2023-05-24 22:31:59 UTC  
> Url: https://github.com/boostorg/locale/pull/175#issuecomment-1562010809  

## [Codecov](https://app.codecov.io/gh/boostorg/locale/pull/175?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#175](https://app.codecov.io/gh/boostorg/locale/pull/175?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (fcf50b5) into [develop](https://app.codecov.io/gh/boostorg/locale/commit/51df38ff1771e852705775b57a912479995141c3?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (51df38f) will **increase** coverage by `0.02%`.  
> The diff coverage is `92.10%`.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/locale/pull/175/graphs/tree.svg?width=650&height=150&src=pr&token=vF6mWvgGUn&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/locale/pull/175?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #175      +/-   ##  
===========================================  
+ Coverage    91.49%   91.52%   +0.02%       
===========================================  
  Files          112      112                
  Lines         9759     9777      +18       
===========================================  
+ Hits          8929     8948      +19       
+ Misses         830      829       -1       
```  
  
  
| [Impacted Files](https://app.codecov.io/gh/boostorg/locale/pull/175?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [src/boost/locale/std/std\_backend.cpp](https://app.codecov.io/gh/boostorg/locale/pull/175?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-c3JjL2Jvb3N0L2xvY2FsZS9zdGQvc3RkX2JhY2tlbmQuY3Bw) | `83.65% <84.21%> (+0.79%)` | :arrow_up: |  
| [test/test\_generator.cpp](https://app.codecov.io/gh/boostorg/locale/pull/175?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC90ZXN0X2dlbmVyYXRvci5jcHA=) | `99.36% <100.00%> (+0.08%)` | :arrow_up: |  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/locale/pull/175?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/locale/pull/175?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [51df38f...fcf50b5](https://app.codecov.io/gh/boostorg/locale/pull/175?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---
