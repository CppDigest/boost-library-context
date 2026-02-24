# #779 value_to/from supports described structs and enums [Merged]

> Username: grisumbras  
> Created at: 2022-10-20 00:02:16 UTC  
> Updated at: 2022-10-20 19:50:45 UTC  
> Merged at: 2022-10-20 19:49:03 UTC  
> Closed at: 2022-10-20 19:49:03 UTC  
> Url: https://github.com/boostorg/json/pull/779  

Fix #626

---

## Comment 1

> Username: cppalliance-bot  
> Created_at: 2022-10-20 01:14:38 UTC  
> Url: https://github.com/boostorg/json/pull/779#issuecomment-1284762225  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/779/pullrequest-condensed-02151cb.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/779/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/779/pullrequest.html)

---

## Comment 2

> Username: denzor200  
> Created_at: 2022-10-20 07:30:08 UTC  
> Url: https://github.com/boostorg/json/pull/779#issuecomment-1285071699  

Looks interesting and useful. How it will work with inherited described structs?

---

## Comment 3

> Username: cppalliance-bot  
> Created_at: 2022-10-20 14:07:22 UTC  
> Url: https://github.com/boostorg/json/pull/779#issuecomment-1285612828  

An automated preview of the documentation is available at [https://779.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html](https://779.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html)

---

## Comment 4

> Username: cppalliance-bot  
> Created_at: 2022-10-20 14:37:22 UTC  
> Url: https://github.com/boostorg/json/pull/779#issuecomment-1285661206  

An automated preview of the documentation is available at [https://779.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html](https://779.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html)

---

## Comment 5

> Username: cppalliance-bot  
> Created_at: 2022-10-20 14:52:19 UTC  
> Url: https://github.com/boostorg/json/pull/779#issuecomment-1285683531  

An automated preview of the documentation is available at [https://779.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html](https://779.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html)

---

## Comment 6

> Username: cppalliance-bot  
> Created_at: 2022-10-20 15:59:38 UTC  
> Url: https://github.com/boostorg/json/pull/779#issuecomment-1285802531  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/779/pullrequest-condensed-1989263.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/779/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/779/pullrequest.html)

---

## Comment 7

> Username: codecov[bot]  
> Created_at: 2022-10-20 17:16:45 UTC  
> Updated_at: 2022-10-20 18:27:15 UTC  
> Url: https://github.com/boostorg/json/pull/779#issuecomment-1285895290  

# [Codecov](https://codecov.io/gh/boostorg/json/pull/779?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#779](https://codecov.io/gh/boostorg/json/pull/779?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (76e1dba) into [develop](https://codecov.io/gh/boostorg/json/commit/fde4e878773ece73df1546cbe9cc03c7172ed749?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (fde4e87) will **not change** coverage.  
> The diff coverage is `n/a`.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/json/pull/779/graphs/tree.svg?width=650&height=150&src=pr&token=HNiMmIjyKi&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://codecov.io/gh/boostorg/json/pull/779?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@           Coverage Diff            @@  
##           develop     #779   +/-   ##  
========================================  
  Coverage    98.98%   98.98%             
========================================  
  Files           70       70             
  Lines         6732     6732             
========================================  
  Hits          6664     6664             
  Misses          68       68             
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/json/pull/779?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/json/detail/value\_from.hpp](https://codecov.io/gh/boostorg/json/pull/779/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9qc29uL2RldGFpbC92YWx1ZV9mcm9tLmhwcA==) | `100.00% <ø> (ø)` | |  
| [include/boost/json/detail/value\_to.hpp](https://codecov.io/gh/boostorg/json/pull/779/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9qc29uL2RldGFpbC92YWx1ZV90by5ocHA=) | `100.00% <ø> (ø)` | |  
| [include/boost/json/impl/conversion.hpp](https://codecov.io/gh/boostorg/json/pull/779/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9qc29uL2ltcGwvY29udmVyc2lvbi5ocHA=) | `100.00% <ø> (ø)` | |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/json/pull/779?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/json/pull/779?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [fde4e87...76e1dba](https://codecov.io/gh/boostorg/json/pull/779?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---

## Comment 8

> Username: cppalliance-bot  
> Created_at: 2022-10-20 18:12:20 UTC  
> Url: https://github.com/boostorg/json/pull/779#issuecomment-1285954329  

An automated preview of the documentation is available at [https://779.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html](https://779.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html)

---

## Comment 9

> Username: cppalliance-bot  
> Created_at: 2022-10-20 19:14:37 UTC  
> Url: https://github.com/boostorg/json/pull/779#issuecomment-1286020284  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/779/pullrequest-condensed-8cce85a.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/779/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/779/pullrequest.html)

---
