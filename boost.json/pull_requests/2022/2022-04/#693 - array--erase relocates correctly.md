# #693 array::erase relocates correctly [Closed]

> Username: vinniefalco  
> Created at: 2022-04-13 12:50:18 UTC  
> Updated at: 2022-04-24 00:19:37 UTC  
> Closed at: 2022-04-24 00:19:37 UTC  
> Url: https://github.com/boostorg/json/pull/693  

This needs additional tests in array.cpp

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2022-04-13 13:08:52 UTC  
> Updated_at: 2022-04-23 22:43:37 UTC  
> Url: https://github.com/boostorg/json/pull/693#issuecomment-1098030132  

# [Codecov](https://codecov.io/gh/boostorg/json/pull/693?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#693](https://codecov.io/gh/boostorg/json/pull/693?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (2121dcb) into [develop](https://codecov.io/gh/boostorg/json/commit/83c364afaf31b58cfe8b97ab159c406c508f47c9?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (83c364a) will **increase** coverage by `0.00%`.  
> The diff coverage is `25.00%`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/json/pull/693/graphs/tree.svg?width=650&height=150&src=pr&token=HNiMmIjyKi&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://codecov.io/gh/boostorg/json/pull/693?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@           Coverage Diff            @@  
##           develop     #693   +/-   ##  
========================================  
  Coverage    99.08%   99.08%             
========================================  
  Files           69       69             
  Lines         6546     6547    +1       
========================================  
+ Hits          6486     6487    +1       
  Misses          60       60             
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/json/pull/693?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/json/impl/serialize.ipp](https://codecov.io/gh/boostorg/json/pull/693/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9qc29uL2ltcGwvc2VyaWFsaXplLmlwcA==) | `87.34% <25.00%> (+0.16%)` | :arrow_up: |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/json/pull/693?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/json/pull/693?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [83c364a...2121dcb](https://codecov.io/gh/boostorg/json/pull/693?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).

---

## Comment 2

> Username: cppalliance-bot  
> Created_at: 2022-04-13 14:04:40 UTC  
> Url: https://github.com/boostorg/json/pull/693#issuecomment-1098092865  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/693/pullrequest-condensed-a376150.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/693/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/693/pullrequest.html)

---

## Comment 3

> Username: vinniefalco  
> Created_at: 2022-04-14 19:12:50 UTC  
> Url: https://github.com/boostorg/json/pull/693#issuecomment-1099542197  

@grisumbras ?

---

## Comment 4

> Username: cppalliance-bot  
> Created_at: 2022-04-23 23:29:32 UTC  
> Url: https://github.com/boostorg/json/pull/693#issuecomment-1107665173  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/693/pullrequest-condensed-56ec8b9.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/693/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/693/pullrequest.html)

---
