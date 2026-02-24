# #980 deprecate type aliases [Merged]

> Username: grisumbras  
> Created at: 2024-02-08 11:07:10 UTC  
> Updated at: 2024-02-09 13:35:36 UTC  
> Merged at: 2024-02-09 13:35:24 UTC  
> Closed at: 2024-02-09 13:35:24 UTC  
> Url: https://github.com/boostorg/json/pull/980  

Fix #881

---

## Comment 1

> Username: cppalliance-bot  
> Created_at: 2024-02-08 11:18:07 UTC  
> Url: https://github.com/boostorg/json/pull/980#issuecomment-1933865075  

An automated preview of the documentation is available at [https://980.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html](https://980.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html)

---

## Comment 2

> Username: cppalliance-bot  
> Created_at: 2024-02-08 13:22:07 UTC  
> Url: https://github.com/boostorg/json/pull/980#issuecomment-1934115728  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/980/pullrequest-condensed-f722c7e.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/980/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/980/pullrequest.html)

---

## Comment 3

> Username: cppalliance-bot  
> Created_at: 2024-02-08 17:38:05 UTC  
> Url: https://github.com/boostorg/json/pull/980#issuecomment-1934626662  

An automated preview of the documentation is available at [https://980.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html](https://980.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html)

---

## Comment 4

> Username: codecov[bot]  
> Created_at: 2024-02-08 18:03:49 UTC  
> Updated_at: 2024-02-09 11:02:07 UTC  
> Url: https://github.com/boostorg/json/pull/980#issuecomment-1934671482  

## [Codecov](https://app.codecov.io/gh/boostorg/json/pull/980?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
All modified and coverable lines are covered by tests :white_check_mark:  
> Comparison is base [(`411b9a9`)](https://app.codecov.io/gh/boostorg/json/commit/411b9a9201b8e969cab803ff9ff829cac93c782d?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) 93.08% compared to head [(`bacc644`)](https://app.codecov.io/gh/boostorg/json/pull/980?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) 93.08%.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/json/pull/980/graphs/tree.svg?width=650&height=150&src=pr&token=HNiMmIjyKi&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/json/pull/980?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@           Coverage Diff            @@  
##           develop     #980   +/-   ##  
========================================  
  Coverage    93.08%   93.08%             
========================================  
  Files           87       87             
  Lines         8122     8122             
========================================  
  Hits          7560     7560             
  Misses         562      562             
```  
  
  
| [Files](https://app.codecov.io/gh/boostorg/json/pull/980?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/json/array.hpp](https://app.codecov.io/gh/boostorg/json/pull/980?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9qc29uL2FycmF5LmhwcA==) | `100.00% <ø> (ø)` | |  
| [include/boost/json/basic\_parser.hpp](https://app.codecov.io/gh/boostorg/json/pull/980?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9qc29uL2Jhc2ljX3BhcnNlci5ocHA=) | `100.00% <ø> (ø)` | |  
| [include/boost/json/basic\_parser\_impl.hpp](https://app.codecov.io/gh/boostorg/json/pull/980?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9qc29uL2Jhc2ljX3BhcnNlcl9pbXBsLmhwcA==) | `98.45% <100.00%> (ø)` | |  
| [include/boost/json/detail/default\_resource.hpp](https://app.codecov.io/gh/boostorg/json/pull/980?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9qc29uL2RldGFpbC9kZWZhdWx0X3Jlc291cmNlLmhwcA==) | `83.33% <ø> (ø)` | |  
| [include/boost/json/detail/impl/except.ipp](https://app.codecov.io/gh/boostorg/json/pull/980?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9qc29uL2RldGFpbC9pbXBsL2V4Y2VwdC5pcHA=) | `100.00% <100.00%> (ø)` | |  
| [include/boost/json/detail/impl/handler.ipp](https://app.codecov.io/gh/boostorg/json/pull/980?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9qc29uL2RldGFpbC9pbXBsL2hhbmRsZXIuaXBw) | `100.00% <ø> (ø)` | |  
| [include/boost/json/detail/parse\_into.hpp](https://app.codecov.io/gh/boostorg/json/pull/980?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9qc29uL2RldGFpbC9wYXJzZV9pbnRvLmhwcA==) | `100.00% <100.00%> (ø)` | |  
| [include/boost/json/detail/shared\_resource.hpp](https://app.codecov.io/gh/boostorg/json/pull/980?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9qc29uL2RldGFpbC9zaGFyZWRfcmVzb3VyY2UuaHBw) | `100.00% <ø> (ø)` | |  
| [include/boost/json/detail/value.hpp](https://app.codecov.io/gh/boostorg/json/pull/980?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9qc29uL2RldGFpbC92YWx1ZS5ocHA=) | `100.00% <ø> (ø)` | |  
| [include/boost/json/detail/value\_to.hpp](https://app.codecov.io/gh/boostorg/json/pull/980?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9qc29uL2RldGFpbC92YWx1ZV90by5ocHA=) | `100.00% <100.00%> (ø)` | |  
| ... and [21 more](https://app.codecov.io/gh/boostorg/json/pull/980?src=pr&el=tree-more&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | |  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/json/pull/980?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/json/pull/980?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [411b9a9...bacc644](https://app.codecov.io/gh/boostorg/json/pull/980?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---

## Comment 5

> Username: cppalliance-bot  
> Created_at: 2024-02-08 20:01:00 UTC  
> Url: https://github.com/boostorg/json/pull/980#issuecomment-1934846427  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/980/pullrequest-condensed-05617d0.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/980/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/980/pullrequest.html)

---

## Comment 6

> Username: cppalliance-bot  
> Created_at: 2024-02-09 10:28:08 UTC  
> Url: https://github.com/boostorg/json/pull/980#issuecomment-1935676393  

An automated preview of the documentation is available at [https://980.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html](https://980.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html)

---

## Comment 7

> Username: cppalliance-bot  
> Created_at: 2024-02-09 11:37:14 UTC  
> Url: https://github.com/boostorg/json/pull/980#issuecomment-1935769978  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/980/pullrequest-condensed-ee13410.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/980/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/980/pullrequest.html)

---
