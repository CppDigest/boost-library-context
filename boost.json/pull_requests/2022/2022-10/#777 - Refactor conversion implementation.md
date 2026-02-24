# #777 Refactor conversion implementation [Merged]

> Username: grisumbras  
> Created at: 2022-10-19 14:26:23 UTC  
> Updated at: 2022-10-19 20:10:24 UTC  
> Merged at: 2022-10-19 20:10:13 UTC  
> Closed at: 2022-10-19 20:10:13 UTC  
> Url: https://github.com/boostorg/json/pull/777  

* Address Vinnie's comments in #686.  
* Merge `value_traits.hpp` into `impl/conversion.hpp`.

---

## Comment 1

> Username: cppalliance-bot  
> Created_at: 2022-10-19 14:32:21 UTC  
> Url: https://github.com/boostorg/json/pull/777#issuecomment-1284114825  

An automated preview of the documentation is available at [https://777.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html](https://777.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html)

---

## Comment 2

> Username: cppalliance-bot  
> Created_at: 2022-10-19 15:39:40 UTC  
> Url: https://github.com/boostorg/json/pull/777#issuecomment-1284214211  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/777/pullrequest-condensed-e6f2453.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/777/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/777/pullrequest.html)

---

## Comment 3

> Username: codecov[bot]  
> Created_at: 2022-10-19 19:25:04 UTC  
> Updated_at: 2022-10-19 19:36:23 UTC  
> Url: https://github.com/boostorg/json/pull/777#issuecomment-1284471919  

# [Codecov](https://codecov.io/gh/boostorg/json/pull/777?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#777](https://codecov.io/gh/boostorg/json/pull/777?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (413c672) into [develop](https://codecov.io/gh/boostorg/json/commit/f4031f3ef51eddce73fd4ccfadc4b96b9100e546?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (f4031f3) will **not change** coverage.  
> The diff coverage is `n/a`.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/json/pull/777/graphs/tree.svg?width=650&height=150&src=pr&token=HNiMmIjyKi&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://codecov.io/gh/boostorg/json/pull/777?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@           Coverage Diff            @@  
##           develop     #777   +/-   ##  
========================================  
  Coverage    98.99%   98.99%             
========================================  
  Files           70       70             
  Lines         6734     6734             
========================================  
  Hits          6666     6666             
  Misses          68       68             
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/json/pull/777?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/json/detail/value\_from.hpp](https://codecov.io/gh/boostorg/json/pull/777/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9qc29uL2RldGFpbC92YWx1ZV9mcm9tLmhwcA==) | `100.00% <ø> (ø)` | |  
| [include/boost/json/detail/value\_to.hpp](https://codecov.io/gh/boostorg/json/pull/777/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9qc29uL2RldGFpbC92YWx1ZV90by5ocHA=) | `100.00% <ø> (ø)` | |  
| [include/boost/json/impl/conversion.hpp](https://codecov.io/gh/boostorg/json/pull/777/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9qc29uL2ltcGwvY29udmVyc2lvbi5ocHA=) | `100.00% <ø> (ø)` | |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/json/pull/777?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/json/pull/777?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [f4031f3...413c672](https://codecov.io/gh/boostorg/json/pull/777?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---
