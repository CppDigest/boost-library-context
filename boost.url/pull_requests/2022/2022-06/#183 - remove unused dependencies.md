# #183 remove unused dependencies [Merged]

> Username: alandefreitas  
> Created at: 2022-06-04 01:12:27 UTC  
> Updated at: 2022-06-08 13:38:48 UTC  
> Merged at: 2022-06-08 13:38:26 UTC  
> Closed at: 2022-06-08 13:38:26 UTC  
> Url: https://github.com/boostorg/url/pull/183  



---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2022-06-04 06:22:17 UTC  
> Url: https://github.com/boostorg/url/pull/183#issuecomment-1146548153  

# [Codecov](https://codecov.io/gh/CPPAlliance/url/pull/183?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance) Report  
> Merging [#183](https://codecov.io/gh/CPPAlliance/url/pull/183?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance) (62b9cf2) into [develop](https://codecov.io/gh/CPPAlliance/url/commit/173a32b4476edaf18e96711a601134403060fcd8?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance) (173a32b) will **not change** coverage.  
> The diff coverage is `n/a`.  
  
[![Impacted file tree graph](https://codecov.io/gh/CPPAlliance/url/pull/183/graphs/tree.svg?width=650&height=150&src=pr&token=jUkdNr0eJi&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance)](https://codecov.io/gh/CPPAlliance/url/pull/183?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance)  
  
```diff  
@@           Coverage Diff            @@  
##           develop     #183   +/-   ##  
========================================  
  Coverage    96.73%   96.73%             
========================================  
  Files          119      119             
  Lines         6056     6056             
========================================  
  Hits          5858     5858             
  Misses         198      198             
```  
  
  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/CPPAlliance/url/pull/183?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/CPPAlliance/url/pull/183?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance). Last update [173a32b...62b9cf2](https://codecov.io/gh/CPPAlliance/url/pull/183?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance).

---

## Comment 2

> Username: vinniefalco  
> Created_at: 2022-06-08 13:23:03 UTC  
> Updated_at: 2022-06-08 13:23:20 UTC  
> Url: https://github.com/boostorg/url/pull/183#issuecomment-1149910546  

yes. what about the Jamfiles?

---

## Comment 3

> Username: alandefreitas  
> Created_at: 2022-06-08 13:24:49 UTC  
> Updated_at: 2022-06-08 13:25:00 UTC  
> Url: https://github.com/boostorg/url/pull/183#issuecomment-1149912672  

> yes. what about the Jamfiles?  
  
I think the concept of interface targets doesn't exist in `b2` (or we have never used it).

---
