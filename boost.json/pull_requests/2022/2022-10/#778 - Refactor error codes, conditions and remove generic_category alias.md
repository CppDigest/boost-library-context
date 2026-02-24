# #778 Refactor error codes, conditions and remove generic_category alias [Merged]

> Username: grisumbras  
> Created at: 2022-10-19 14:44:32 UTC  
> Updated at: 2022-10-19 20:10:33 UTC  
> Merged at: 2022-10-19 20:10:13 UTC  
> Closed at: 2022-10-19 20:10:13 UTC  
> Url: https://github.com/boostorg/json/pull/778  

* Fix #756  
* Fix #758

---

## Comment 1

> Username: cppalliance-bot  
> Created_at: 2022-10-19 14:52:27 UTC  
> Url: https://github.com/boostorg/json/pull/778#issuecomment-1284143929  

An automated preview of the documentation is available at [https://778.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html](https://778.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html)

---

## Comment 2

> Username: codecov[bot]  
> Created_at: 2022-10-19 15:02:45 UTC  
> Updated_at: 2022-10-19 15:14:07 UTC  
> Url: https://github.com/boostorg/json/pull/778#issuecomment-1284158911  

# [Codecov](https://codecov.io/gh/boostorg/json/pull/778?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#778](https://codecov.io/gh/boostorg/json/pull/778?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (fde4e87) into [develop](https://codecov.io/gh/boostorg/json/commit/f4031f3ef51eddce73fd4ccfadc4b96b9100e546?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (f4031f3) will **decrease** coverage by `0.00%`.  
> The diff coverage is `100.00%`.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/json/pull/778/graphs/tree.svg?width=650&height=150&src=pr&token=HNiMmIjyKi&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://codecov.io/gh/boostorg/json/pull/778?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #778      +/-   ##  
===========================================  
- Coverage    98.99%   98.98%   -0.01%       
===========================================  
  Files           70       70                
  Lines         6734     6732       -2       
===========================================  
- Hits          6666     6664       -2       
  Misses          68       68                
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/json/pull/778?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/json/detail/value\_from.hpp](https://codecov.io/gh/boostorg/json/pull/778/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9qc29uL2RldGFpbC92YWx1ZV9mcm9tLmhwcA==) | `100.00% <ø> (ø)` | |  
| [include/boost/json/detail/value\_to.hpp](https://codecov.io/gh/boostorg/json/pull/778/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9qc29uL2RldGFpbC92YWx1ZV90by5ocHA=) | `100.00% <ø> (ø)` | |  
| [include/boost/json/impl/conversion.hpp](https://codecov.io/gh/boostorg/json/pull/778/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9qc29uL2ltcGwvY29udmVyc2lvbi5ocHA=) | `100.00% <ø> (ø)` | |  
| [include/boost/json/system\_error.hpp](https://codecov.io/gh/boostorg/json/pull/778/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9qc29uL3N5c3RlbV9lcnJvci5ocHA=) | `100.00% <ø> (ø)` | |  
| [include/boost/json/impl/error.ipp](https://codecov.io/gh/boostorg/json/pull/778/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9qc29uL2ltcGwvZXJyb3IuaXBw) | `100.00% <100.00%> (ø)` | |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/json/pull/778?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/json/pull/778?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [f4031f3...fde4e87](https://codecov.io/gh/boostorg/json/pull/778?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---

## Comment 3

> Username: cppalliance-bot  
> Created_at: 2022-10-19 16:51:09 UTC  
> Url: https://github.com/boostorg/json/pull/778#issuecomment-1284303466  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/778/pullrequest-condensed-b65c4c2.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/778/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/778/pullrequest.html)

---
