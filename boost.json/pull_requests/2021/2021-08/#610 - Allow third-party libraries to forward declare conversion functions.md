# #610 Allow third-party libraries to forward declare conversion functions [Merged]

> Username: grisumbras  
> Created at: 2021-08-17 20:48:04 UTC  
> Updated at: 2021-10-20 19:37:35 UTC  
> Merged at: 2021-10-20 19:35:59 UTC  
> Closed at: 2021-10-20 19:35:59 UTC  
> Url: https://github.com/boostorg/json/pull/610  

Fixes #549 and #550

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2021-10-06 12:41:58 UTC  
> Updated_at: 2021-10-20 19:35:39 UTC  
> Url: https://github.com/boostorg/json/pull/610#issuecomment-936162607  

# [Codecov](https://codecov.io/gh/boostorg/json/pull/610?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#610](https://codecov.io/gh/boostorg/json/pull/610?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (a00876b) into [develop](https://codecov.io/gh/boostorg/json/commit/06513b0736308e330de462e0eac1bfd207cbc72e?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (06513b0) will **increase** coverage by `0.00%`.  
> The diff coverage is `100.00%`.  
  
> :exclamation: Current head a00876b differs from pull request most recent head 31dd295. Consider uploading reports for the commit 31dd295 to get more accurate results  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/json/pull/610/graphs/tree.svg?width=650&height=150&src=pr&token=HNiMmIjyKi&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://codecov.io/gh/boostorg/json/pull/610?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@           Coverage Diff            @@  
##           develop     #610   +/-   ##  
========================================  
  Coverage    99.05%   99.05%             
========================================  
  Files           68       68             
  Lines         6119     6121    +2       
========================================  
+ Hits          6061     6063    +2       
  Misses          58       58             
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/json/pull/610?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/json/value\_from.hpp](https://codecov.io/gh/boostorg/json/pull/610/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9qc29uL3ZhbHVlX2Zyb20uaHBw) | `100.00% <100.00%> (ø)` | |  
| [include/boost/json/value\_to.hpp](https://codecov.io/gh/boostorg/json/pull/610/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9qc29uL3ZhbHVlX3RvLmhwcA==) | `100.00% <100.00%> (ø)` | |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/json/pull/610?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/json/pull/610?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [06513b0...31dd295](https://codecov.io/gh/boostorg/json/pull/610?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).

---

## Comment 2

> Username: cppalliance-bot  
> Created_at: 2021-10-06 13:34:04 UTC  
> Url: https://github.com/boostorg/json/pull/610#issuecomment-936245372  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/610/pullrequest-condensed-6bcc62e.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/610/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/610/pullrequest.html)

---

## Comment 3

> Username: cppalliance-bot  
> Created_at: 2021-10-06 18:51:20 UTC  
> Url: https://github.com/boostorg/json/pull/610#issuecomment-936897162  

An automated preview of the documentation is available at [https://610.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html](https://610.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html)

---

## Comment 4

> Username: cppalliance-bot  
> Created_at: 2021-10-06 20:13:58 UTC  
> Url: https://github.com/boostorg/json/pull/610#issuecomment-937046526  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/610/pullrequest-condensed-c86bd7c.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/610/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/610/pullrequest.html)

---

## Comment 5

> Username: cppalliance-bot  
> Created_at: 2021-10-08 12:36:31 UTC  
> Url: https://github.com/boostorg/json/pull/610#issuecomment-938608885  

An automated preview of the documentation is available at [https://610.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html](https://610.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html)

---

## Comment 6

> Username: cppalliance-bot  
> Created_at: 2021-10-08 13:44:03 UTC  
> Url: https://github.com/boostorg/json/pull/610#issuecomment-938654893  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/610/pullrequest-condensed-af47036.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/610/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/610/pullrequest.html)

---

## Comment 7

> Username: cppalliance-bot  
> Created_at: 2021-10-08 20:56:33 UTC  
> Url: https://github.com/boostorg/json/pull/610#issuecomment-939111201  

An automated preview of the documentation is available at [https://610.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html](https://610.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html)

---

## Comment 8

> Username: cppalliance-bot  
> Created_at: 2021-10-08 21:59:04 UTC  
> Url: https://github.com/boostorg/json/pull/610#issuecomment-939138039  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/610/pullrequest-condensed-9b59ce7.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/610/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/610/pullrequest.html)

---

## Comment 9

> Username: cppalliance-bot  
> Created_at: 2021-10-13 13:31:23 UTC  
> Url: https://github.com/boostorg/json/pull/610#issuecomment-942311506  

An automated preview of the documentation is available at [https://610.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html](https://610.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html)

---

## Comment 10

> Username: cppalliance-bot  
> Created_at: 2021-10-13 15:46:01 UTC  
> Url: https://github.com/boostorg/json/pull/610#issuecomment-942437351  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/610/pullrequest-condensed-98b813c.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/610/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/610/pullrequest.html)

---

## Comment 11

> Username: cppalliance-bot  
> Created_at: 2021-10-19 10:51:30 UTC  
> Url: https://github.com/boostorg/json/pull/610#issuecomment-946604859  

An automated preview of the documentation is available at [https://610.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html](https://610.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html)

---

## Comment 12

> Username: cppalliance-bot  
> Created_at: 2021-10-19 12:18:59 UTC  
> Url: https://github.com/boostorg/json/pull/610#issuecomment-946664577  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/610/pullrequest-condensed-e1631e1.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/610/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/610/pullrequest.html)

---

## Comment 13

> Username: cppalliance-bot  
> Created_at: 2021-10-20 11:50:46 UTC  
> Url: https://github.com/boostorg/json/pull/610#issuecomment-947589049  

An automated preview of the documentation is available at [https://610.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html](https://610.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html)

---

## Comment 14

> Username: cppalliance-bot  
> Created_at: 2021-10-20 12:49:21 UTC  
> Url: https://github.com/boostorg/json/pull/610#issuecomment-947632925  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/610/pullrequest-condensed-6c1a07c.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/610/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/610/pullrequest.html)

---
