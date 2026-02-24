# #137 add stub library for backward compatibility [Merged]

> Username: JeffGarland  
> Created at: 2020-03-16 01:57:27 UTC  
> Updated at: 2022-02-12 02:13:55 UTC  
> Merged at: 2020-03-16 04:04:00 UTC  
> Closed at: 2020-03-16 04:04:00 UTC  
> Url: https://github.com/boostorg/date_time/pull/137  



---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2020-03-16 03:32:22 UTC  
> Url: https://github.com/boostorg/date_time/pull/137#issuecomment-599328833  

# [Codecov](https://codecov.io/gh/boostorg/date_time/pull/137?src=pr&el=h1) Report  
> Merging [#137](https://codecov.io/gh/boostorg/date_time/pull/137?src=pr&el=desc) into [develop](https://codecov.io/gh/boostorg/date_time/commit/d8c7a59ed9f1b355dcc7bd2143a8868ceec237bc?src=pr&el=desc) will **not change** coverage.  
> The diff coverage is `n/a`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/date_time/pull/137/graphs/tree.svg?width=650&token=nDoh7t8f6g&height=150&src=pr)](https://codecov.io/gh/boostorg/date_time/pull/137?src=pr&el=tree)  
  
```diff  
@@           Coverage Diff            @@  
##           develop     #137   +/-   ##  
========================================  
  Coverage    52.68%   52.68%             
========================================  
  Files           76       76             
  Lines         4491     4491             
  Branches      2243     2243             
========================================  
  Hits          2366     2366             
  Misses         393      393             
  Partials      1732     1732  
```  
  
  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/date_time/pull/137?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/date_time/pull/137?src=pr&el=footer). Last update [d8c7a59...90d812d](https://codecov.io/gh/boostorg/date_time/pull/137?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---

## Review 2 [Commented]

> Username: pdimov  
> Created_at: 2020-03-16 12:50:16 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/date_time/pull/137#pullrequestreview-375183078  

📁 build/Jamfile.v2

```diff
  10 |+ project boost/date_time
  11 |+     : requirements
  12 |+         <define>DATE_TIME_INLINE
```

> Username: pdimov  
> Created_at: 2020-03-16 12:50:16 UTC  
> Url: https://github.com/boostorg/date_time/pull/137#discussion_r393000025  

Is this define necessary? (And similarly in usage-requirements.)

> Username: JeffGarland  
> Created_at: 2020-03-16 14:04:03 UTC  
> Url: https://github.com/boostorg/date_time/pull/137#discussion_r393044887  

No it's not -- it's legacy from what was there previously.  I'll clean it up when I clean up a bunch of other now dead code.  Thx!


---
