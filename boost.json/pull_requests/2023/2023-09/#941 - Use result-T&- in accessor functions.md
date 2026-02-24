# #941 Use result<T&> in accessor functions [Merged]

> Username: grisumbras  
> Created at: 2023-09-21 21:31:49 UTC  
> Updated at: 2024-06-19 11:33:25 UTC  
> Merged at: 2024-06-19 11:32:44 UTC  
> Closed at: 2024-06-19 11:32:44 UTC  
> Url: https://github.com/boostorg/json/pull/941  



---

## Comment 1

> Username: cppalliance-bot  
> Created_at: 2023-09-21 22:50:04 UTC  
> Url: https://github.com/boostorg/json/pull/941#issuecomment-1730449494  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/941/pullrequest-condensed-d1417fc.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/941/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/941/pullrequest.html)

---

## Comment 2

> Username: codecov[bot]  
> Created_at: 2023-09-22 06:56:25 UTC  
> Updated_at: 2024-06-18 00:24:29 UTC  
> Url: https://github.com/boostorg/json/pull/941#issuecomment-1730900665  

## [Codecov](https://app.codecov.io/gh/boostorg/json/pull/941?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
Attention: Patch coverage is `98.30508%` with `4 lines` in your changes missing coverage. Please review.  
> Project coverage is 93.40%. Comparing base [(`3d334a7`)](https://app.codecov.io/gh/boostorg/json/commit/3d334a74e185578b657650cb2a47959b509b2b8d?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) to head [(`12a4e77`)](https://app.codecov.io/gh/boostorg/json/commit/12a4e77e294f534513dbf298df4d5ea0bccad7e1?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/json/pull/941/graphs/tree.svg?width=650&height=150&src=pr&token=HNiMmIjyKi&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/json/pull/941?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #941      +/-   ##  
===========================================  
+ Coverage    93.34%   93.40%   +0.05%       
===========================================  
  Files           87       87                
  Lines         8490     8622     +132       
===========================================  
+ Hits          7925     8053     +128       
- Misses         565      569       +4       
```  
  
  
| [Files](https://app.codecov.io/gh/boostorg/json/pull/941?dropdown=coverage&src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/json/array.hpp](https://app.codecov.io/gh/boostorg/json/pull/941?src=pr&el=tree&filepath=include%2Fboost%2Fjson%2Farray.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9qc29uL2FycmF5LmhwcA==) | `100.00% <ø> (ø)` | |  
| [include/boost/json/impl/array.hpp](https://app.codecov.io/gh/boostorg/json/pull/941?src=pr&el=tree&filepath=include%2Fboost%2Fjson%2Fimpl%2Farray.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9qc29uL2ltcGwvYXJyYXkuaHBw) | `100.00% <100.00%> (ø)` | |  
| [include/boost/json/impl/array.ipp](https://app.codecov.io/gh/boostorg/json/pull/941?src=pr&el=tree&filepath=include%2Fboost%2Fjson%2Fimpl%2Farray.ipp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9qc29uL2ltcGwvYXJyYXkuaXBw) | `100.00% <100.00%> (ø)` | |  
| [include/boost/json/impl/object.hpp](https://app.codecov.io/gh/boostorg/json/pull/941?src=pr&el=tree&filepath=include%2Fboost%2Fjson%2Fimpl%2Fobject.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9qc29uL2ltcGwvb2JqZWN0LmhwcA==) | `100.00% <100.00%> (ø)` | |  
| [include/boost/json/impl/object.ipp](https://app.codecov.io/gh/boostorg/json/pull/941?src=pr&el=tree&filepath=include%2Fboost%2Fjson%2Fimpl%2Fobject.ipp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9qc29uL2ltcGwvb2JqZWN0LmlwcA==) | `100.00% <100.00%> (ø)` | |  
| [include/boost/json/impl/pointer.ipp](https://app.codecov.io/gh/boostorg/json/pull/941?src=pr&el=tree&filepath=include%2Fboost%2Fjson%2Fimpl%2Fpointer.ipp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9qc29uL2ltcGwvcG9pbnRlci5pcHA=) | `100.00% <100.00%> (ø)` | |  
| [include/boost/json/impl/string.hpp](https://app.codecov.io/gh/boostorg/json/pull/941?src=pr&el=tree&filepath=include%2Fboost%2Fjson%2Fimpl%2Fstring.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9qc29uL2ltcGwvc3RyaW5nLmhwcA==) | `98.52% <100.00%> (+0.06%)` | :arrow_up: |  
| [include/boost/json/impl/string.ipp](https://app.codecov.io/gh/boostorg/json/pull/941?src=pr&el=tree&filepath=include%2Fboost%2Fjson%2Fimpl%2Fstring.ipp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9qc29uL2ltcGwvc3RyaW5nLmlwcA==) | `100.00% <100.00%> (ø)` | |  
| [include/boost/json/impl/value.hpp](https://app.codecov.io/gh/boostorg/json/pull/941?src=pr&el=tree&filepath=include%2Fboost%2Fjson%2Fimpl%2Fvalue.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9qc29uL2ltcGwvdmFsdWUuaHBw) | `100.00% <100.00%> (ø)` | |  
| [include/boost/json/object.hpp](https://app.codecov.io/gh/boostorg/json/pull/941?src=pr&el=tree&filepath=include%2Fboost%2Fjson%2Fobject.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9qc29uL29iamVjdC5ocHA=) | `100.00% <ø> (ø)` | |  
| ... and [3 more](https://app.codecov.io/gh/boostorg/json/pull/941?src=pr&el=tree-more&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | |  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/json/pull/941?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/json/pull/941?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [3d334a7...12a4e77](https://app.codecov.io/gh/boostorg/json/pull/941?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---

## Comment 3

> Username: cppalliance-bot  
> Created_at: 2023-09-22 08:52:48 UTC  
> Url: https://github.com/boostorg/json/pull/941#issuecomment-1731057039  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/941/pullrequest-condensed-cbc2d27.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/941/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/941/pullrequest.html)

---

## Comment 4

> Username: cppalliance-bot  
> Created_at: 2023-09-23 15:11:53 UTC  
> Url: https://github.com/boostorg/json/pull/941#issuecomment-1732339208  

An automated preview of the documentation is available at [https://941.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html](https://941.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html)

---

## Comment 5

> Username: cppalliance-bot  
> Created_at: 2023-09-23 16:20:14 UTC  
> Url: https://github.com/boostorg/json/pull/941#issuecomment-1732357189  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/941/pullrequest-condensed-a80ec37.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/941/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/941/pullrequest.html)

---

## Comment 6

> Username: cppalliance-bot  
> Created_at: 2023-09-24 16:11:52 UTC  
> Url: https://github.com/boostorg/json/pull/941#issuecomment-1732610612  

An automated preview of the documentation is available at [https://941.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html](https://941.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html)

---

## Comment 7

> Username: cppalliance-bot  
> Created_at: 2023-09-24 18:12:50 UTC  
> Url: https://github.com/boostorg/json/pull/941#issuecomment-1732635592  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/941/pullrequest-condensed-811c692.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/941/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/941/pullrequest.html)

---

## Comment 8

> Username: cppalliance-bot  
> Created_at: 2023-09-25 06:36:51 UTC  
> Url: https://github.com/boostorg/json/pull/941#issuecomment-1733008353  

An automated preview of the documentation is available at [https://941.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html](https://941.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html)

---

## Comment 9

> Username: cppalliance-bot  
> Created_at: 2023-09-25 07:45:11 UTC  
> Url: https://github.com/boostorg/json/pull/941#issuecomment-1733099476  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/941/pullrequest-condensed-18c8092.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/941/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/941/pullrequest.html)

---

## Comment 10

> Username: cppalliance-bot  
> Created_at: 2023-09-29 09:46:51 UTC  
> Url: https://github.com/boostorg/json/pull/941#issuecomment-1740612708  

An automated preview of the documentation is available at [https://941.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html](https://941.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html)

---

## Comment 11

> Username: cppalliance-bot  
> Created_at: 2023-09-29 11:58:20 UTC  
> Url: https://github.com/boostorg/json/pull/941#issuecomment-1740772050  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/941/pullrequest-condensed-b3dfa26.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/941/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/941/pullrequest.html)

---

## Comment 12

> Username: cppalliance-bot  
> Created_at: 2023-09-30 05:16:53 UTC  
> Url: https://github.com/boostorg/json/pull/941#issuecomment-1741681658  

An automated preview of the documentation is available at [https://941.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html](https://941.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html)

---

## Comment 13

> Username: cppalliance-bot  
> Created_at: 2023-09-30 07:27:29 UTC  
> Url: https://github.com/boostorg/json/pull/941#issuecomment-1741703802  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/941/pullrequest-condensed-8dc9c38.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/941/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/941/pullrequest.html)

---

## Comment 14

> Username: cppalliance-bot  
> Created_at: 2024-05-13 14:02:51 UTC  
> Url: https://github.com/boostorg/json/pull/941#issuecomment-2107661981  

An automated preview of the documentation is available at [https://941.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html](https://941.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html)

---

## Comment 15

> Username: cppalliance-bot  
> Created_at: 2024-05-13 17:22:47 UTC  
> Url: https://github.com/boostorg/json/pull/941#issuecomment-2108322982  

An automated preview of the documentation is available at [https://941.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html](https://941.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html)

---

## Comment 16

> Username: cppalliance-bot  
> Created_at: 2024-05-13 18:32:25 UTC  
> Url: https://github.com/boostorg/json/pull/941#issuecomment-2108538965  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/941/pullrequest-condensed-1c5efc4.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/941/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/941/pullrequest.html)

---

## Comment 17

> Username: cppalliance-bot  
> Created_at: 2024-06-17 11:22:34 UTC  
> Url: https://github.com/boostorg/json/pull/941#issuecomment-2173126945  

An automated preview of the documentation is available at [https://941.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html](https://941.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html)

---

## Comment 18

> Username: cppalliance-bot  
> Created_at: 2024-06-17 12:02:31 UTC  
> Url: https://github.com/boostorg/json/pull/941#issuecomment-2173211317  

An automated preview of the documentation is available at [https://941.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html](https://941.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html)

---

## Comment 19

> Username: cppalliance-bot  
> Created_at: 2024-06-17 13:11:59 UTC  
> Url: https://github.com/boostorg/json/pull/941#issuecomment-2173363383  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/941/pullrequest-condensed-b9fe758.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/941/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/941/pullrequest.html)

---

## Comment 20

> Username: cppalliance-bot  
> Created_at: 2024-06-17 13:47:35 UTC  
> Url: https://github.com/boostorg/json/pull/941#issuecomment-2173443438  

An automated preview of the documentation is available at [https://941.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html](https://941.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html)

---

## Comment 21

> Username: cppalliance-bot  
> Created_at: 2024-06-17 14:57:01 UTC  
> Url: https://github.com/boostorg/json/pull/941#issuecomment-2173641805  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/941/pullrequest-condensed-91c570c.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/941/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/941/pullrequest.html)

---

## Comment 22

> Username: cppalliance-bot  
> Created_at: 2024-06-17 18:02:36 UTC  
> Url: https://github.com/boostorg/json/pull/941#issuecomment-2174010507  

An automated preview of the documentation is available at [https://941.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html](https://941.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html)

---

## Comment 23

> Username: cppalliance-bot  
> Created_at: 2024-06-17 19:12:02 UTC  
> Url: https://github.com/boostorg/json/pull/941#issuecomment-2174234568  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/941/pullrequest-condensed-b48b32b.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/941/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/941/pullrequest.html)

---

## Comment 24

> Username: cppalliance-bot  
> Created_at: 2024-06-18 06:42:32 UTC  
> Url: https://github.com/boostorg/json/pull/941#issuecomment-2175242010  

An automated preview of the documentation is available at [https://941.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html](https://941.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html)

---

## Comment 25

> Username: cppalliance-bot  
> Created_at: 2024-06-18 07:52:02 UTC  
> Url: https://github.com/boostorg/json/pull/941#issuecomment-2175436356  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/941/pullrequest-condensed-7fb90c1.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/941/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/941/pullrequest.html)

---

## Comment 26

> Username: cppalliance-bot  
> Created_at: 2024-06-19 06:22:27 UTC  
> Url: https://github.com/boostorg/json/pull/941#issuecomment-2177842126  

An automated preview of the documentation is available at [https://941.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html](https://941.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html)

---

## Comment 27

> Username: cppalliance-bot  
> Created_at: 2024-06-19 07:32:02 UTC  
> Url: https://github.com/boostorg/json/pull/941#issuecomment-2177957035  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/941/pullrequest-condensed-9a4205d.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/941/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/941/pullrequest.html)

---
