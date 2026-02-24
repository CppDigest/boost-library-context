# #859 Refactor/throw system error [Closed]

> Username: grisumbras  
> Created at: 2023-02-13 10:45:42 UTC  
> Updated at: 2023-06-02 13:24:42 UTC  
> Closed at: 2023-06-02 13:24:37 UTC  
> Url: https://github.com/boostorg/json/pull/859  



---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2023-02-13 11:02:13 UTC  
> Updated_at: 2023-06-02 08:36:27 UTC  
> Url: https://github.com/boostorg/json/pull/859#issuecomment-1427743446  

## [Codecov](https://app.codecov.io/gh/boostorg/json/pull/859?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#859](https://app.codecov.io/gh/boostorg/json/pull/859?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (5ade3d9) into [develop](https://app.codecov.io/gh/boostorg/json/commit/35dc1b964e2987998b2276a9d533bbd4b4825b01?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (35dc1b9) will **decrease** coverage by `0.05%`.  
> The diff coverage is `100.00%`.  
  
> :exclamation: Current head 5ade3d9 differs from pull request most recent head e7b193a. Consider uploading reports for the commit e7b193a to get more accurate results  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/json/pull/859/graphs/tree.svg?width=650&height=150&src=pr&token=HNiMmIjyKi&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/json/pull/859?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #859      +/-   ##  
===========================================  
- Coverage    99.25%   99.20%   -0.05%       
===========================================  
  Files           71       70       -1       
  Lines         6937     6897      -40       
===========================================  
- Hits          6885     6842      -43       
- Misses          52       55       +3       
```  
  
  
| [Impacted Files](https://app.codecov.io/gh/boostorg/json/pull/859?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/json/array.hpp](https://app.codecov.io/gh/boostorg/json/pull/859?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9qc29uL2FycmF5LmhwcA==) | `100.00% <ø> (ø)` | |  
| [include/boost/json/detail/impl/except.ipp](https://app.codecov.io/gh/boostorg/json/pull/859?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9qc29uL2RldGFpbC9pbXBsL2V4Y2VwdC5pcHA=) | `100.00% <ø> (ø)` | |  
| [include/boost/json/detail/value\_to.hpp](https://app.codecov.io/gh/boostorg/json/pull/859?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9qc29uL2RldGFpbC92YWx1ZV90by5ocHA=) | `100.00% <ø> (ø)` | |  
| [include/boost/json/impl/value.ipp](https://app.codecov.io/gh/boostorg/json/pull/859?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9qc29uL2ltcGwvdmFsdWUuaXBw) | `100.00% <ø> (ø)` | |  
| [include/boost/json/object.hpp](https://app.codecov.io/gh/boostorg/json/pull/859?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9qc29uL29iamVjdC5ocHA=) | `100.00% <ø> (ø)` | |  
| [include/boost/json/detail/impl/string\_impl.ipp](https://app.codecov.io/gh/boostorg/json/pull/859?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9qc29uL2RldGFpbC9pbXBsL3N0cmluZ19pbXBsLmlwcA==) | `99.19% <100.00%> (+0.07%)` | :arrow_up: |  
| [include/boost/json/impl/array.hpp](https://app.codecov.io/gh/boostorg/json/pull/859?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9qc29uL2ltcGwvYXJyYXkuaHBw) | `100.00% <100.00%> (ø)` | |  
| [include/boost/json/impl/array.ipp](https://app.codecov.io/gh/boostorg/json/pull/859?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9qc29uL2ltcGwvYXJyYXkuaXBw) | `100.00% <100.00%> (ø)` | |  
| [include/boost/json/impl/error.ipp](https://app.codecov.io/gh/boostorg/json/pull/859?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9qc29uL2ltcGwvZXJyb3IuaXBw) | `100.00% <100.00%> (ø)` | |  
| [include/boost/json/impl/null\_resource.ipp](https://app.codecov.io/gh/boostorg/json/pull/859?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9qc29uL2ltcGwvbnVsbF9yZXNvdXJjZS5pcHA=) | `80.00% <100.00%> (ø)` | |  
| ... and [6 more](https://app.codecov.io/gh/boostorg/json/pull/859?src=pr&el=tree-more&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | |  
  
... and [13 files with indirect coverage changes](https://app.codecov.io/gh/boostorg/json/pull/859/indirect-changes?src=pr&el=tree-more&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/json/pull/859?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/json/pull/859?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [35dc1b9...e7b193a](https://app.codecov.io/gh/boostorg/json/pull/859?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---

## Comment 2

> Username: cppalliance-bot  
> Created_at: 2023-02-13 11:59:45 UTC  
> Url: https://github.com/boostorg/json/pull/859#issuecomment-1427820913  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/859/pullrequest-condensed-5f92603.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/859/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/859/pullrequest.html)

---

## Comment 3

> Username: cppalliance-bot  
> Created_at: 2023-06-01 16:03:07 UTC  
> Url: https://github.com/boostorg/json/pull/859#issuecomment-1572328901  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/859/pullrequest-condensed-48b40e8.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/859/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/859/pullrequest.html)

---

## Comment 4

> Username: cppalliance-bot  
> Created_at: 2023-06-01 18:35:37 UTC  
> Url: https://github.com/boostorg/json/pull/859#issuecomment-1572586864  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/859/pullrequest-condensed-d781eda.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/859/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/859/pullrequest.html)

---

## Comment 5

> Username: cppalliance-bot  
> Created_at: 2023-06-02 07:13:07 UTC  
> Url: https://github.com/boostorg/json/pull/859#issuecomment-1573265797  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/859/pullrequest-condensed-762c05d.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/859/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/859/pullrequest.html)

---

## Comment 6

> Username: cppalliance-bot  
> Created_at: 2023-06-02 09:50:36 UTC  
> Url: https://github.com/boostorg/json/pull/859#issuecomment-1573460450  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/859/pullrequest-condensed-4c36bda.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/859/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/859/pullrequest.html)

---
