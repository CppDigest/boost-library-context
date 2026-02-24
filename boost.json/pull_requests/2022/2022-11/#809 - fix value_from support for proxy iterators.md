# #809 fix value_from support for proxy iterators [Merged]

> Username: grisumbras  
> Created at: 2022-11-16 20:19:48 UTC  
> Updated at: 2022-11-20 16:27:52 UTC  
> Merged at: 2022-11-20 16:27:44 UTC  
> Closed at: 2022-11-20 16:27:44 UTC  
> Url: https://github.com/boostorg/json/pull/809  

Fix #616

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2022-11-16 20:40:25 UTC  
> Updated_at: 2022-11-20 16:27:00 UTC  
> Url: https://github.com/boostorg/json/pull/809#issuecomment-1317641506  

# [Codecov](https://codecov.io/gh/boostorg/json/pull/809?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#809](https://codecov.io/gh/boostorg/json/pull/809?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (ce03ebb) into [develop](https://codecov.io/gh/boostorg/json/commit/f993635f4d2ad02f7870bb6933fc17ebbf601d05?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (f993635) will **increase** coverage by `0.00%`.  
> The diff coverage is `100.00%`.  
  
> :exclamation: Current head ce03ebb differs from pull request most recent head 893df14. Consider uploading reports for the commit 893df14 to get more accurate results  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/json/pull/809/graphs/tree.svg?width=650&height=150&src=pr&token=HNiMmIjyKi&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://codecov.io/gh/boostorg/json/pull/809?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@           Coverage Diff            @@  
##           develop     #809   +/-   ##  
========================================  
  Coverage    99.00%   99.00%             
========================================  
  Files           71       71             
  Lines         6831     6832    +1       
========================================  
+ Hits          6763     6764    +1       
  Misses          68       68             
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/json/pull/809?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/json/impl/conversion.hpp](https://codecov.io/gh/boostorg/json/pull/809/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9qc29uL2ltcGwvY29udmVyc2lvbi5ocHA=) | `100.00% <ø> (ø)` | |  
| [include/boost/json/detail/value\_from.hpp](https://codecov.io/gh/boostorg/json/pull/809/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9qc29uL2RldGFpbC92YWx1ZV9mcm9tLmhwcA==) | `100.00% <100.00%> (ø)` | |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/json/pull/809?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/json/pull/809?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [f993635...893df14](https://codecov.io/gh/boostorg/json/pull/809?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---

## Comment 2

> Username: cppalliance-bot  
> Created_at: 2022-11-16 22:37:36 UTC  
> Url: https://github.com/boostorg/json/pull/809#issuecomment-1317767465  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/809/pullrequest-condensed-f44c71e.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/809/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/809/pullrequest.html)

---
