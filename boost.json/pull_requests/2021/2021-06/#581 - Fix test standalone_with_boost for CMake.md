# #581 Fix test standalone_with_boost for CMake [Closed]

> Username: grisumbras  
> Created at: 2021-06-18 10:41:20 UTC  
> Updated at: 2021-06-23 05:40:57 UTC  
> Closed at: 2021-06-23 05:40:50 UTC  
> Url: https://github.com/boostorg/json/pull/581  

Fix #577

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2021-06-18 11:00:58 UTC  
> Updated_at: 2021-06-18 11:26:38 UTC  
> Url: https://github.com/boostorg/json/pull/581#issuecomment-863954268  

# [Codecov](https://codecov.io/gh/boostorg/json/pull/581?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#581](https://codecov.io/gh/boostorg/json/pull/581?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (409238e) into [develop](https://codecov.io/gh/boostorg/json/commit/351603c9b2dee7a80c8433841b50f7294131b25a?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (351603c) will **decrease** coverage by `0.00%`.  
> The diff coverage is `n/a`.  
  
> :exclamation: Current head 409238e differs from pull request most recent head 2f4400b. Consider uploading reports for the commit 2f4400b to get more accurate results  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/json/pull/581/graphs/tree.svg?width=650&height=150&src=pr&token=HNiMmIjyKi&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://codecov.io/gh/boostorg/json/pull/581?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #581      +/-   ##  
===========================================  
- Coverage    99.05%   99.05%   -0.01%       
===========================================  
  Files           68       68                
  Lines         6115     6114       -1       
===========================================  
- Hits          6057     6056       -1       
  Misses          58       58                
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/json/pull/581?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/json/impl/error.ipp](https://codecov.io/gh/boostorg/json/pull/581/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9qc29uL2ltcGwvZXJyb3IuaXBw) | `100.00% <0.00%> (ø)` | |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/json/pull/581?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/json/pull/581?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [351603c...2f4400b](https://codecov.io/gh/boostorg/json/pull/581?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).

---

## Comment 2

> Username: grisumbras  
> Created_at: 2021-06-18 12:29:01 UTC  
> Url: https://github.com/boostorg/json/pull/581#issuecomment-864002941  

@pdimov can you test if this change solves the issue?  
@vinniefalco maybe we need CI jobs that build a shared library and test it?

---

## Comment 3

> Username: cppalliance-bot  
> Created_at: 2021-06-18 12:39:02 UTC  
> Url: https://github.com/boostorg/json/pull/581#issuecomment-864008451  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/581/pullrequest-condensed-7e8a65a.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/581/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/581/pullrequest.html)

---

## Comment 4

> Username: pdimov  
> Created_at: 2021-06-18 12:53:34 UTC  
> Url: https://github.com/boostorg/json/pull/581#issuecomment-864016492  

Seems to work. Can you please attach the tests to the `tests` target? As in https://github.com/boostorg/property_tree/commit/591990729134a031339c21270b3ed70c632ab906#diff-33394812ba204689144fd2f80832db83853ba1cb32403edb4e15fe4893e675fd

---
