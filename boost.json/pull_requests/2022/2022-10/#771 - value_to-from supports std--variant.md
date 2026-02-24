# #771 value_to/from supports std::variant [Merged]

> Username: grisumbras  
> Created at: 2022-10-17 14:32:45 UTC  
> Updated at: 2022-10-18 15:24:40 UTC  
> Merged at: 2022-10-18 15:24:35 UTC  
> Closed at: 2022-10-18 15:24:35 UTC  
> Url: https://github.com/boostorg/json/pull/771  

Fix #517

---

## Comment 1

> Username: cppalliance-bot  
> Created_at: 2022-10-17 14:42:07 UTC  
> Url: https://github.com/boostorg/json/pull/771#issuecomment-1280974866  

An automated preview of the documentation is available at [https://771.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html](https://771.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html)

---

## Comment 2

> Username: cppalliance-bot  
> Created_at: 2022-10-17 15:44:39 UTC  
> Url: https://github.com/boostorg/json/pull/771#issuecomment-1281081323  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/771/pullrequest-condensed-3ba0a4d.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/771/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/771/pullrequest.html)

---

## Comment 3

> Username: cppalliance-bot  
> Created_at: 2022-10-18 13:06:41 UTC  
> Url: https://github.com/boostorg/json/pull/771#issuecomment-1282357691  

An automated preview of the documentation is available at [https://771.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html](https://771.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html)

---

## Comment 4

> Username: codecov[bot]  
> Created_at: 2022-10-18 13:12:24 UTC  
> Updated_at: 2022-10-18 13:12:28 UTC  
> Url: https://github.com/boostorg/json/pull/771#issuecomment-1282365173  

# [Codecov](https://codecov.io/gh/boostorg/json/pull/771?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#771](https://codecov.io/gh/boostorg/json/pull/771?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (6ff132c) into [develop](https://codecov.io/gh/boostorg/json/commit/8daa656b5e1f3bac089a5058e079ac43b047345f?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (8daa656) will **not change** coverage.  
> The diff coverage is `n/a`.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/json/pull/771/graphs/tree.svg?width=650&height=150&src=pr&token=HNiMmIjyKi&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://codecov.io/gh/boostorg/json/pull/771?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@           Coverage Diff            @@  
##           develop     #771   +/-   ##  
========================================  
  Coverage    98.99%   98.99%             
========================================  
  Files           70       70             
  Lines         6734     6734             
========================================  
  Hits          6666     6666             
  Misses          68       68             
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/json/pull/771?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/json/detail/value\_from.hpp](https://codecov.io/gh/boostorg/json/pull/771/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9qc29uL2RldGFpbC92YWx1ZV9mcm9tLmhwcA==) | `100.00% <ø> (ø)` | |  
| [include/boost/json/detail/value\_to.hpp](https://codecov.io/gh/boostorg/json/pull/771/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9qc29uL2RldGFpbC92YWx1ZV90by5ocHA=) | `100.00% <ø> (ø)` | |  
| [include/boost/json/value\_from.hpp](https://codecov.io/gh/boostorg/json/pull/771/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9qc29uL3ZhbHVlX2Zyb20uaHBw) | `100.00% <ø> (ø)` | |  
| [include/boost/json/value\_to.hpp](https://codecov.io/gh/boostorg/json/pull/771/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9qc29uL3ZhbHVlX3RvLmhwcA==) | `100.00% <ø> (ø)` | |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/json/pull/771?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/json/pull/771?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [8daa656...6ff132c](https://codecov.io/gh/boostorg/json/pull/771?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---

## Comment 5

> Username: cppalliance-bot  
> Created_at: 2022-10-18 14:14:36 UTC  
> Url: https://github.com/boostorg/json/pull/771#issuecomment-1282468058  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/771/pullrequest-condensed-fd02af8.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/771/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/771/pullrequest.html)

---
