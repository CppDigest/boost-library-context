# #784 rvalue-ref accessors [Merged]

> Username: grisumbras  
> Created at: 2022-10-24 11:49:46 UTC  
> Updated at: 2022-10-24 14:34:48 UTC  
> Merged at: 2022-10-24 14:34:42 UTC  
> Closed at: 2022-10-24 14:34:42 UTC  
> Url: https://github.com/boostorg/json/pull/784  

Fix #757

---

## Comment 1

> Username: cppalliance-bot  
> Created_at: 2022-10-24 11:56:58 UTC  
> Url: https://github.com/boostorg/json/pull/784#issuecomment-1288924521  

An automated preview of the documentation is available at [https://784.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html](https://784.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html)

---

## Comment 2

> Username: codecov[bot]  
> Created_at: 2022-10-24 12:04:02 UTC  
> Updated_at: 2022-10-24 13:17:57 UTC  
> Url: https://github.com/boostorg/json/pull/784#issuecomment-1288931841  

# [Codecov](https://codecov.io/gh/boostorg/json/pull/784?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#784](https://codecov.io/gh/boostorg/json/pull/784?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (77c94f5) into [develop](https://codecov.io/gh/boostorg/json/commit/213726054b6f4c5b3e542045d31dd171db952c65?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (2137260) will **increase** coverage by `0.00%`.  
> The diff coverage is `100.00%`.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/json/pull/784/graphs/tree.svg?width=650&height=150&src=pr&token=HNiMmIjyKi&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://codecov.io/gh/boostorg/json/pull/784?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@           Coverage Diff            @@  
##           develop     #784   +/-   ##  
========================================  
  Coverage    98.99%   98.99%             
========================================  
  Files           70       71    +1       
  Lines         6734     6763   +29       
========================================  
+ Hits          6666     6695   +29       
  Misses          68       68             
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/json/pull/784?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/json/array.hpp](https://codecov.io/gh/boostorg/json/pull/784/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9qc29uL2FycmF5LmhwcA==) | `100.00% <ø> (ø)` | |  
| [include/boost/json/object.hpp](https://codecov.io/gh/boostorg/json/pull/784/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9qc29uL29iamVjdC5ocHA=) | `100.00% <ø> (ø)` | |  
| [include/boost/json/impl/array.hpp](https://codecov.io/gh/boostorg/json/pull/784/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9qc29uL2ltcGwvYXJyYXkuaHBw) | `100.00% <100.00%> (ø)` | |  
| [include/boost/json/impl/object.hpp](https://codecov.io/gh/boostorg/json/pull/784/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9qc29uL2ltcGwvb2JqZWN0LmhwcA==) | `100.00% <100.00%> (ø)` | |  
| [include/boost/json/impl/pointer.ipp](https://codecov.io/gh/boostorg/json/pull/784/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9qc29uL2ltcGwvcG9pbnRlci5pcHA=) | `100.00% <100.00%> (ø)` | |  
| [include/boost/json/impl/value.hpp](https://codecov.io/gh/boostorg/json/pull/784/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9qc29uL2ltcGwvdmFsdWUuaHBw) | `100.00% <100.00%> (ø)` | |  
| [include/boost/json/value.hpp](https://codecov.io/gh/boostorg/json/pull/784/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9qc29uL3ZhbHVlLmhwcA==) | `98.94% <100.00%> (+0.03%)` | :arrow_up: |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/json/pull/784?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/json/pull/784?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [2137260...77c94f5](https://codecov.io/gh/boostorg/json/pull/784?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---

## Comment 3

> Username: cppalliance-bot  
> Created_at: 2022-10-24 13:59:39 UTC  
> Url: https://github.com/boostorg/json/pull/784#issuecomment-1289080949  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/784/pullrequest-condensed-44ff945.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/784/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/784/pullrequest.html)

---
