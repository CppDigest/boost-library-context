# #800 refactor parse(istream&, ec&) [Merged]

> Username: grisumbras  
> Created at: 2022-11-05 16:08:41 UTC  
> Updated at: 2022-11-06 10:55:29 UTC  
> Merged at: 2022-11-06 10:54:49 UTC  
> Closed at: 2022-11-06 10:54:49 UTC  
> Url: https://github.com/boostorg/json/pull/800  



---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2022-11-05 16:54:00 UTC  
> Updated_at: 2022-11-06 09:49:37 UTC  
> Url: https://github.com/boostorg/json/pull/800#issuecomment-1304587446  

# [Codecov](https://codecov.io/gh/boostorg/json/pull/800?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#800](https://codecov.io/gh/boostorg/json/pull/800?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (4d22409) into [develop](https://codecov.io/gh/boostorg/json/commit/340442779ff91dc8ba1ec77c52dc4d83327c0e2a?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (3404427) will **increase** coverage by `0.00%`.  
> The diff coverage is `100.00%`.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/json/pull/800/graphs/tree.svg?width=650&height=150&src=pr&token=HNiMmIjyKi&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://codecov.io/gh/boostorg/json/pull/800?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@           Coverage Diff            @@  
##           develop     #800   +/-   ##  
========================================  
  Coverage    99.00%   99.00%             
========================================  
  Files           71       71             
  Lines         6824     6831    +7       
========================================  
+ Hits          6756     6763    +7       
  Misses          68       68             
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/json/pull/800?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/json/value.hpp](https://codecov.io/gh/boostorg/json/pull/800/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9qc29uL3ZhbHVlLmhwcA==) | `98.94% <ø> (ø)` | |  
| [include/boost/json/impl/parse.ipp](https://codecov.io/gh/boostorg/json/pull/800/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9qc29uL2ltcGwvcGFyc2UuaXBw) | `100.00% <100.00%> (ø)` | |  
| [include/boost/json/impl/value.ipp](https://codecov.io/gh/boostorg/json/pull/800/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9qc29uL2ltcGwvdmFsdWUuaXBw) | `100.00% <100.00%> (ø)` | |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/json/pull/800?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/json/pull/800?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [3404427...4d22409](https://codecov.io/gh/boostorg/json/pull/800?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---

## Comment 2

> Username: cppalliance-bot  
> Created_at: 2022-11-05 17:24:37 UTC  
> Url: https://github.com/boostorg/json/pull/800#issuecomment-1304595328  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/800/pullrequest-condensed-71a52b5.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/800/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/800/pullrequest.html)

---

## Comment 3

> Username: vinniefalco  
> Created_at: 2022-11-05 17:44:14 UTC  
> Url: https://github.com/boostorg/json/pull/800#issuecomment-1304598825  

should this return `result<value>`?

---

## Comment 4

> Username: cppalliance-bot  
> Created_at: 2022-11-05 21:59:38 UTC  
> Url: https://github.com/boostorg/json/pull/800#issuecomment-1304649393  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/800/pullrequest-condensed-ddfa7e6.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/800/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/800/pullrequest.html)

---

## Comment 5

> Username: cppalliance-bot  
> Created_at: 2022-11-06 10:34:38 UTC  
> Url: https://github.com/boostorg/json/pull/800#issuecomment-1304769391  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/800/pullrequest-condensed-52d8fea.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/800/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/800/pullrequest.html)

---
