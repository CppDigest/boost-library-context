# #863 non-allocating category messages [Merged]

> Username: grisumbras  
> Created at: 2023-03-01 09:32:44 UTC  
> Updated at: 2023-03-01 13:06:17 UTC  
> Merged at: 2023-03-01 10:02:51 UTC  
> Closed at: 2023-03-01 10:02:51 UTC  
> Url: https://github.com/boostorg/json/pull/863  

Fix #862

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2023-03-01 09:55:23 UTC  
> Updated_at: 2023-03-01 09:55:30 UTC  
> Url: https://github.com/boostorg/json/pull/863#issuecomment-1449739979  

# [Codecov](https://codecov.io/gh/boostorg/json/pull/863?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#863](https://codecov.io/gh/boostorg/json/pull/863?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (4d4d5fc) into [develop](https://codecov.io/gh/boostorg/json/commit/33d733d13cf5d8f530f8b01bc787d5b90c008c0b?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (33d733d) will **increase** coverage by `0.00%`.  
> The diff coverage is `100.00%`.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/json/pull/863/graphs/tree.svg?width=650&height=150&src=pr&token=HNiMmIjyKi&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://codecov.io/gh/boostorg/json/pull/863?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@           Coverage Diff            @@  
##           develop     #863   +/-   ##  
========================================  
  Coverage    99.19%   99.19%             
========================================  
  Files           70       70             
  Lines         6849     6851    +2       
========================================  
+ Hits          6794     6796    +2       
  Misses          55       55             
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/json/pull/863?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/json/impl/error.ipp](https://codecov.io/gh/boostorg/json/pull/863?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9qc29uL2ltcGwvZXJyb3IuaXBw) | `100.00% <100.00%> (ø)` | |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/json/pull/863?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/json/pull/863?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [33d733d...4d4d5fc](https://codecov.io/gh/boostorg/json/pull/863?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---

## Comment 2

> Username: cppalliance-bot  
> Created_at: 2023-03-01 11:41:32 UTC  
> Url: https://github.com/boostorg/json/pull/863#issuecomment-1449973299  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/863/pullrequest-condensed-c39d6e6.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/863/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/863/pullrequest.html)

---

## Comment 3

> Username: vinniefalco  
> Created_at: 2023-03-01 12:52:51 UTC  
> Url: https://github.com/boostorg/json/pull/863#issuecomment-1450104531  

`make_error_code` needs to be inline and visible, like this:   
https://github.com/vinniefalco/http_proto/blob/871c20cc38e337880363a5efa2649a22806e1bd0/include/boost/http_proto/impl/error.hpp#L85

---

## Comment 4

> Username: grisumbras  
> Created_at: 2023-03-01 12:57:34 UTC  
> Url: https://github.com/boostorg/json/pull/863#issuecomment-1450110791  

Why inline?

---

## Comment 5

> Username: vinniefalco  
> Created_at: 2023-03-01 13:06:17 UTC  
> Url: https://github.com/boostorg/json/pull/863#issuecomment-1450125650  

performance

---
