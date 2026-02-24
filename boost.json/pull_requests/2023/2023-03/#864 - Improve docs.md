# #864 Improve docs [Merged]

> Username: grisumbras  
> Created at: 2023-03-01 13:21:13 UTC  
> Updated at: 2023-04-02 19:40:42 UTC  
> Merged at: 2023-04-02 18:44:41 UTC  
> Closed at: 2023-04-02 18:44:41 UTC  
> Url: https://github.com/boostorg/json/pull/864  

Fix #193  
Fix #376  
Fix #393  
Fix #412  
Fix #589  
Fix #691  
Fix #743  
Fix problem mentioned in #815 (unclear how to build the library)  
Fix #841  
Fix #846

---

## Comment 1

> Username: cppalliance-bot  
> Created_at: 2023-03-01 13:26:52 UTC  
> Url: https://github.com/boostorg/json/pull/864#issuecomment-1450153797  

An automated preview of the documentation is available at [https://864.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html](https://864.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html)

---

## Comment 2

> Username: codecov[bot]  
> Created_at: 2023-03-01 13:38:10 UTC  
> Updated_at: 2023-04-02 18:42:13 UTC  
> Url: https://github.com/boostorg/json/pull/864#issuecomment-1450167441  

## [Codecov](https://codecov.io/gh/boostorg/json/pull/864?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#864](https://codecov.io/gh/boostorg/json/pull/864?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (94c5d74) into [develop](https://codecov.io/gh/boostorg/json/commit/9b06d4a4107f7df58a516987474705fef8839f64?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (9b06d4a) will **not change** coverage.  
> The diff coverage is `n/a`.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/json/pull/864/graphs/tree.svg?width=650&height=150&src=pr&token=HNiMmIjyKi&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://codecov.io/gh/boostorg/json/pull/864?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@           Coverage Diff            @@  
##           develop     #864   +/-   ##  
========================================  
  Coverage    99.19%   99.19%             
========================================  
  Files           70       70             
  Lines         6851     6851             
========================================  
  Hits          6796     6796             
  Misses          55       55             
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/json/pull/864?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/json/array.hpp](https://codecov.io/gh/boostorg/json/pull/864?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9qc29uL2FycmF5LmhwcA==) | `100.00% <ø> (ø)` | |  
| [include/boost/json/monotonic\_resource.hpp](https://codecov.io/gh/boostorg/json/pull/864?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9qc29uL21vbm90b25pY19yZXNvdXJjZS5ocHA=) | `100.00% <ø> (ø)` | |  
| [include/boost/json/object.hpp](https://codecov.io/gh/boostorg/json/pull/864?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9qc29uL29iamVjdC5ocHA=) | `100.00% <ø> (ø)` | |  
| [include/boost/json/parser.hpp](https://codecov.io/gh/boostorg/json/pull/864?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9qc29uL3BhcnNlci5ocHA=) | `100.00% <ø> (ø)` | |  
| [include/boost/json/static\_resource.hpp](https://codecov.io/gh/boostorg/json/pull/864?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9qc29uL3N0YXRpY19yZXNvdXJjZS5ocHA=) | `100.00% <ø> (ø)` | |  
| [include/boost/json/storage\_ptr.hpp](https://codecov.io/gh/boostorg/json/pull/864?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9qc29uL3N0b3JhZ2VfcHRyLmhwcA==) | `100.00% <ø> (ø)` | |  
| [include/boost/json/stream\_parser.hpp](https://codecov.io/gh/boostorg/json/pull/864?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9qc29uL3N0cmVhbV9wYXJzZXIuaHBw) | `100.00% <ø> (ø)` | |  
| [include/boost/json/string.hpp](https://codecov.io/gh/boostorg/json/pull/864?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9qc29uL3N0cmluZy5ocHA=) | `100.00% <ø> (ø)` | |  
| [include/boost/json/value.hpp](https://codecov.io/gh/boostorg/json/pull/864?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9qc29uL3ZhbHVlLmhwcA==) | `98.93% <ø> (ø)` | |  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://codecov.io/gh/boostorg/json/pull/864?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/json/pull/864?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [9b06d4a...94c5d74](https://codecov.io/gh/boostorg/json/pull/864?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---

## Comment 3

> Username: cppalliance-bot  
> Created_at: 2023-03-01 14:34:45 UTC  
> Url: https://github.com/boostorg/json/pull/864#issuecomment-1450252889  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/864/pullrequest-condensed-b9257ee.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/864/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/864/pullrequest.html)

---

## Comment 4

> Username: cppalliance-bot  
> Created_at: 2023-03-03 12:46:48 UTC  
> Url: https://github.com/boostorg/json/pull/864#issuecomment-1453482427  

An automated preview of the documentation is available at [https://864.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html](https://864.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html)

---

## Comment 5

> Username: cppalliance-bot  
> Created_at: 2023-03-03 13:54:51 UTC  
> Url: https://github.com/boostorg/json/pull/864#issuecomment-1453566042  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/864/pullrequest-condensed-e62fbf2.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/864/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/864/pullrequest.html)

---

## Comment 6

> Username: cppalliance-bot  
> Created_at: 2023-03-14 14:16:56 UTC  
> Url: https://github.com/boostorg/json/pull/864#issuecomment-1468186405  

An automated preview of the documentation is available at [https://864.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html](https://864.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html)

---

## Comment 7

> Username: cppalliance-bot  
> Created_at: 2023-03-14 15:24:45 UTC  
> Url: https://github.com/boostorg/json/pull/864#issuecomment-1468313557  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/864/pullrequest-condensed-e72bc5e.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/864/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/864/pullrequest.html)

---

## Comment 8

> Username: cppalliance-bot  
> Created_at: 2023-03-23 12:36:43 UTC  
> Url: https://github.com/boostorg/json/pull/864#issuecomment-1481121472  

An automated preview of the documentation is available at [https://864.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html](https://864.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html)

---

## Comment 9

> Username: cppalliance-bot  
> Created_at: 2023-03-24 15:46:43 UTC  
> Url: https://github.com/boostorg/json/pull/864#issuecomment-1483022158  

An automated preview of the documentation is available at [https://864.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html](https://864.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html)

---

## Comment 10

> Username: cppalliance-bot  
> Created_at: 2023-03-27 10:11:43 UTC  
> Url: https://github.com/boostorg/json/pull/864#issuecomment-1484872182  

An automated preview of the documentation is available at [https://864.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html](https://864.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html)

---

## Comment 11

> Username: cppalliance-bot  
> Created_at: 2023-04-02 18:31:39 UTC  
> Url: https://github.com/boostorg/json/pull/864#issuecomment-1493410055  

An automated preview of the documentation is available at [https://864.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html](https://864.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html)

---

## Comment 12

> Username: cppalliance-bot  
> Created_at: 2023-04-02 19:40:41 UTC  
> Url: https://github.com/boostorg/json/pull/864#issuecomment-1493423473  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/864/pullrequest-condensed-5fd51cf.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/864/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/864/pullrequest.html)

---
