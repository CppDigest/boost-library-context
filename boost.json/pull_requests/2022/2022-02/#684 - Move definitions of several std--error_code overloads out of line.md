# #684 Move definitions of several std::error_code overloads out of line [Merged]

> Username: grisumbras  
> Created at: 2022-02-21 12:33:32 UTC  
> Updated at: 2022-02-24 13:25:57 UTC  
> Merged at: 2022-02-24 13:25:51 UTC  
> Closed at: 2022-02-24 13:25:51 UTC  
> Url: https://github.com/boostorg/json/pull/684  

Fix #682

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2022-02-21 12:59:25 UTC  
> Updated_at: 2022-02-24 13:25:25 UTC  
> Url: https://github.com/boostorg/json/pull/684#issuecomment-1046851597  

# [Codecov](https://codecov.io/gh/boostorg/json/pull/684?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#684](https://codecov.io/gh/boostorg/json/pull/684?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (56e9b42) into [develop](https://codecov.io/gh/boostorg/json/commit/eb6441465a930727f8bf740f0c358a7b6fca1a8d?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (eb64414) will **not change** coverage.  
> The diff coverage is `100.00%`.  
  
> :exclamation: Current head 56e9b42 differs from pull request most recent head 59cccb1. Consider uploading reports for the commit 59cccb1 to get more accurate results  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/json/pull/684/graphs/tree.svg?width=650&height=150&src=pr&token=HNiMmIjyKi&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://codecov.io/gh/boostorg/json/pull/684?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@           Coverage Diff            @@  
##           develop     #684   +/-   ##  
========================================  
  Coverage    99.05%   99.05%             
========================================  
  Files           69       68    -1       
  Lines         6375     6375             
========================================  
  Hits          6315     6315             
  Misses          60       60             
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/json/pull/684?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/json/parser.hpp](https://codecov.io/gh/boostorg/json/pull/684/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9qc29uL3BhcnNlci5ocHA=) | `100.00% <ø> (ø)` | |  
| [include/boost/json/stream\_parser.hpp](https://codecov.io/gh/boostorg/json/pull/684/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9qc29uL3N0cmVhbV9wYXJzZXIuaHBw) | `100.00% <ø> (ø)` | |  
| [include/boost/json/impl/parse.ipp](https://codecov.io/gh/boostorg/json/pull/684/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9qc29uL2ltcGwvcGFyc2UuaXBw) | `100.00% <100.00%> (ø)` | |  
| [include/boost/json/impl/parser.ipp](https://codecov.io/gh/boostorg/json/pull/684/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9qc29uL2ltcGwvcGFyc2VyLmlwcA==) | `100.00% <100.00%> (ø)` | |  
| [include/boost/json/impl/stream\_parser.ipp](https://codecov.io/gh/boostorg/json/pull/684/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9qc29uL2ltcGwvc3RyZWFtX3BhcnNlci5pcHA=) | `100.00% <100.00%> (ø)` | |  
| [include/boost/json/parse.hpp](https://codecov.io/gh/boostorg/json/pull/684/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9qc29uL3BhcnNlLmhwcA==) | | |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/json/pull/684?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/json/pull/684?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [eb64414...59cccb1](https://codecov.io/gh/boostorg/json/pull/684?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).

---

## Comment 2

> Username: cppalliance-bot  
> Created_at: 2022-02-21 15:08:29 UTC  
> Url: https://github.com/boostorg/json/pull/684#issuecomment-1046975135  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/684/pullrequest-condensed-4ffebe2.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/684/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/684/pullrequest.html)

---
