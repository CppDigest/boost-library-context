# #1002 use Python implementation of docca [Merged]

> Username: grisumbras  
> Created at: 2024-05-03 11:28:46 UTC  
> Updated at: 2024-06-14 16:37:07 UTC  
> Merged at: 2024-06-14 16:36:47 UTC  
> Closed at: 2024-06-14 16:36:47 UTC  
> Url: https://github.com/boostorg/json/pull/1002  



---

## Comment 1

> Username: cppalliance-bot  
> Created_at: 2024-05-03 15:22:11 UTC  
> Url: https://github.com/boostorg/json/pull/1002#issuecomment-2093235018  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/1002/pullrequest-condensed-7f18fa4.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/1002/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/1002/pullrequest.html)

---

## Comment 2

> Username: codecov[bot]  
> Created_at: 2024-05-03 22:47:32 UTC  
> Updated_at: 2024-06-14 16:03:02 UTC  
> Url: https://github.com/boostorg/json/pull/1002#issuecomment-2093856508  

## [Codecov](https://app.codecov.io/gh/boostorg/json/pull/1002?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
All modified and coverable lines are covered by tests :white_check_mark:  
> Project coverage is 93.34%. Comparing base [(`c4af9af`)](https://app.codecov.io/gh/boostorg/json/commit/c4af9affc0d87f87b0fee3fc6d66ec9360d28ae0?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) to head [(`6ab2ad8`)](https://app.codecov.io/gh/boostorg/json/commit/6ab2ad816b8ca117075cc86dcba505b8d8a10ce0?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/json/pull/1002/graphs/tree.svg?width=650&height=150&src=pr&token=HNiMmIjyKi&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/json/pull/1002?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@           Coverage Diff            @@  
##           develop    #1002   +/-   ##  
========================================  
  Coverage    93.34%   93.34%             
========================================  
  Files           87       87             
  Lines         8490     8490             
========================================  
  Hits          7925     7925             
  Misses         565      565             
```  
  
  
| [Files](https://app.codecov.io/gh/boostorg/json/pull/1002?dropdown=coverage&src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/json/array.hpp](https://app.codecov.io/gh/boostorg/json/pull/1002?src=pr&el=tree&filepath=include%2Fboost%2Fjson%2Farray.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9qc29uL2FycmF5LmhwcA==) | `100.00% <ø> (ø)` | |  
| [include/boost/json/basic\_parser.hpp](https://app.codecov.io/gh/boostorg/json/pull/1002?src=pr&el=tree&filepath=include%2Fboost%2Fjson%2Fbasic_parser.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9qc29uL2Jhc2ljX3BhcnNlci5ocHA=) | `100.00% <ø> (ø)` | |  
| [include/boost/json/monotonic\_resource.hpp](https://app.codecov.io/gh/boostorg/json/pull/1002?src=pr&el=tree&filepath=include%2Fboost%2Fjson%2Fmonotonic_resource.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9qc29uL21vbm90b25pY19yZXNvdXJjZS5ocHA=) | `100.00% <ø> (ø)` | |  
| [include/boost/json/object.hpp](https://app.codecov.io/gh/boostorg/json/pull/1002?src=pr&el=tree&filepath=include%2Fboost%2Fjson%2Fobject.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9qc29uL29iamVjdC5ocHA=) | `100.00% <ø> (ø)` | |  
| [include/boost/json/parser.hpp](https://app.codecov.io/gh/boostorg/json/pull/1002?src=pr&el=tree&filepath=include%2Fboost%2Fjson%2Fparser.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9qc29uL3BhcnNlci5ocHA=) | `100.00% <ø> (ø)` | |  
| [include/boost/json/storage\_ptr.hpp](https://app.codecov.io/gh/boostorg/json/pull/1002?src=pr&el=tree&filepath=include%2Fboost%2Fjson%2Fstorage_ptr.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9qc29uL3N0b3JhZ2VfcHRyLmhwcA==) | `100.00% <ø> (ø)` | |  
| [include/boost/json/stream\_parser.hpp](https://app.codecov.io/gh/boostorg/json/pull/1002?src=pr&el=tree&filepath=include%2Fboost%2Fjson%2Fstream_parser.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9qc29uL3N0cmVhbV9wYXJzZXIuaHBw) | `100.00% <ø> (ø)` | |  
| [include/boost/json/string.hpp](https://app.codecov.io/gh/boostorg/json/pull/1002?src=pr&el=tree&filepath=include%2Fboost%2Fjson%2Fstring.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9qc29uL3N0cmluZy5ocHA=) | `100.00% <ø> (ø)` | |  
| [include/boost/json/value.hpp](https://app.codecov.io/gh/boostorg/json/pull/1002?src=pr&el=tree&filepath=include%2Fboost%2Fjson%2Fvalue.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9qc29uL3ZhbHVlLmhwcA==) | `98.92% <100.00%> (ø)` | |  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/json/pull/1002?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/json/pull/1002?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [c4af9af...6ab2ad8](https://app.codecov.io/gh/boostorg/json/pull/1002?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---

## Comment 3

> Username: cppalliance-bot  
> Created_at: 2024-05-03 23:22:11 UTC  
> Url: https://github.com/boostorg/json/pull/1002#issuecomment-2093879508  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/1002/pullrequest-condensed-3c05c81.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/1002/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/1002/pullrequest.html)

---

## Comment 4

> Username: cppalliance-bot  
> Created_at: 2024-05-13 17:10:41 UTC  
> Url: https://github.com/boostorg/json/pull/1002#issuecomment-2108276479  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/1002/pullrequest-condensed-8571adc.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/1002/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/1002/pullrequest.html)

---

## Comment 5

> Username: cppalliance-bot  
> Created_at: 2024-05-28 07:36:50 UTC  
> Url: https://github.com/boostorg/json/pull/1002#issuecomment-2134540457  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/1002/pullrequest-condensed-3066f48.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/1002/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/1002/pullrequest.html)

---

## Comment 6

> Username: cppalliance-bot  
> Created_at: 2024-06-12 07:17:11 UTC  
> Url: https://github.com/boostorg/json/pull/1002#issuecomment-2162284619  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/1002/pullrequest-condensed-ea7aa20.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/1002/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/1002/pullrequest.html)

---

## Comment 7

> Username: cppalliance-bot  
> Created_at: 2024-06-12 10:16:58 UTC  
> Url: https://github.com/boostorg/json/pull/1002#issuecomment-2162640973  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/1002/pullrequest-condensed-9554fa4.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/1002/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/1002/pullrequest.html)

---

## Comment 8

> Username: cppalliance-bot  
> Created_at: 2024-06-12 12:17:02 UTC  
> Url: https://github.com/boostorg/json/pull/1002#issuecomment-2162863337  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/1002/pullrequest-condensed-a2f92b7.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/1002/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/1002/pullrequest.html)

---

## Comment 9

> Username: cppalliance-bot  
> Created_at: 2024-06-13 07:52:08 UTC  
> Url: https://github.com/boostorg/json/pull/1002#issuecomment-2164893865  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/1002/pullrequest-condensed-5f4c5e2.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/1002/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/1002/pullrequest.html)

---

## Comment 10

> Username: cppalliance-bot  
> Created_at: 2024-06-13 12:07:34 UTC  
> Url: https://github.com/boostorg/json/pull/1002#issuecomment-2165469812  

An automated preview of the documentation is available at [https://1002.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html](https://1002.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html)

---

## Comment 11

> Username: cppalliance-bot  
> Created_at: 2024-06-13 13:17:04 UTC  
> Url: https://github.com/boostorg/json/pull/1002#issuecomment-2165647291  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/1002/pullrequest-condensed-6177f6c.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/1002/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/1002/pullrequest.html)

---

## Comment 12

> Username: cppalliance-bot  
> Created_at: 2024-06-13 19:57:27 UTC  
> Url: https://github.com/boostorg/json/pull/1002#issuecomment-2166660932  

An automated preview of the documentation is available at [https://1002.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html](https://1002.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html)

---

## Comment 13

> Username: cppalliance-bot  
> Created_at: 2024-06-13 21:07:00 UTC  
> Url: https://github.com/boostorg/json/pull/1002#issuecomment-2166797016  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/1002/pullrequest-condensed-0a19dea.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/1002/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/1002/pullrequest.html)

---

## Comment 14

> Username: cppalliance-bot  
> Created_at: 2024-06-14 15:27:31 UTC  
> Url: https://github.com/boostorg/json/pull/1002#issuecomment-2168272281  

An automated preview of the documentation is available at [https://1002.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html](https://1002.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html)

---

## Comment 15

> Username: cppalliance-bot  
> Created_at: 2024-06-14 16:37:05 UTC  
> Url: https://github.com/boostorg/json/pull/1002#issuecomment-2168385703  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/1002/pullrequest-condensed-68afd1b.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/1002/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/1002/pullrequest.html)

---
