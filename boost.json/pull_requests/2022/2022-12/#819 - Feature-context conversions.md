# #819 Feature/context conversions [Merged]

> Username: grisumbras  
> Created at: 2022-12-06 19:35:34 UTC  
> Updated at: 2023-06-02 04:28:54 UTC  
> Merged at: 2023-06-02 04:28:40 UTC  
> Closed at: 2023-06-02 04:28:40 UTC  
> Url: https://github.com/boostorg/json/pull/819  

Fix #647  
Fix #854  
Fix #871

---

## Comment 1

> Username: cppalliance-bot  
> Created_at: 2022-12-06 19:41:43 UTC  
> Url: https://github.com/boostorg/json/pull/819#issuecomment-1339914915  

An automated preview of the documentation is available at [https://819.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html](https://819.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html)

---

## Comment 2

> Username: cppalliance-bot  
> Created_at: 2022-12-06 20:49:45 UTC  
> Url: https://github.com/boostorg/json/pull/819#issuecomment-1339989024  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/819/pullrequest-condensed-6d89e0b.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/819/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/819/pullrequest.html)

---

## Comment 3

> Username: cppalliance-bot  
> Created_at: 2022-12-13 13:31:37 UTC  
> Url: https://github.com/boostorg/json/pull/819#issuecomment-1348556391  

An automated preview of the documentation is available at [https://819.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html](https://819.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html)

---

## Comment 4

> Username: cppalliance-bot  
> Created_at: 2022-12-13 14:39:38 UTC  
> Url: https://github.com/boostorg/json/pull/819#issuecomment-1348707303  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/819/pullrequest-condensed-1a42291.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/819/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/819/pullrequest.html)

---

## Review 5 [Commented]

> Username: vinniefalco  
> Created_at: 2022-12-13 15:13:49 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/819#pullrequestreview-1215686750  

📁 include/boost/json/conversion.hpp

```diff
 322 | template<class T>
 323 | struct is_described_enum;
```

> Username: vinniefalco  
> Created_at: 2022-12-13 15:13:49 UTC  
> Url: https://github.com/boostorg/json/pull/819#discussion_r1047291789  

is this header included in `<boost/json.hpp>`?

> Username: grisumbras  
> Created_at: 2022-12-14 08:39:29 UTC  
> Url: https://github.com/boostorg/json/pull/819#discussion_r1048159997  

Yes


---

## Comment 6

> Username: cppalliance-bot  
> Created_at: 2022-12-14 13:51:42 UTC  
> Url: https://github.com/boostorg/json/pull/819#issuecomment-1351394908  

An automated preview of the documentation is available at [https://819.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html](https://819.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html)

---

## Comment 7

> Username: cppalliance-bot  
> Created_at: 2022-12-14 14:11:44 UTC  
> Url: https://github.com/boostorg/json/pull/819#issuecomment-1351436681  

An automated preview of the documentation is available at [https://819.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html](https://819.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html)

---

## Comment 8

> Username: cppalliance-bot  
> Created_at: 2022-12-14 15:19:35 UTC  
> Url: https://github.com/boostorg/json/pull/819#issuecomment-1351616962  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/819/pullrequest-condensed-e3ec650.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/819/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/819/pullrequest.html)

---

## Comment 9

> Username: cppalliance-bot  
> Created_at: 2022-12-14 15:31:40 UTC  
> Url: https://github.com/boostorg/json/pull/819#issuecomment-1351639372  

An automated preview of the documentation is available at [https://819.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html](https://819.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html)

---

## Comment 10

> Username: cppalliance-bot  
> Created_at: 2022-12-14 16:34:39 UTC  
> Url: https://github.com/boostorg/json/pull/819#issuecomment-1351738982  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/819/pullrequest-condensed-723edee.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/819/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/819/pullrequest.html)

---

## Comment 11

> Username: cppalliance-bot  
> Created_at: 2022-12-15 11:26:39 UTC  
> Url: https://github.com/boostorg/json/pull/819#issuecomment-1352923437  

An automated preview of the documentation is available at [https://819.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html](https://819.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html)

---

## Comment 12

> Username: cppalliance-bot  
> Created_at: 2022-12-15 12:34:39 UTC  
> Url: https://github.com/boostorg/json/pull/819#issuecomment-1352996587  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/819/pullrequest-condensed-fa1c56e.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/819/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/819/pullrequest.html)

---

## Comment 13

> Username: cppalliance-bot  
> Created_at: 2023-02-20 10:16:52 UTC  
> Url: https://github.com/boostorg/json/pull/819#issuecomment-1436690888  

An automated preview of the documentation is available at [https://819.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html](https://819.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html)

---

## Comment 14

> Username: cppalliance-bot  
> Created_at: 2023-02-20 11:24:56 UTC  
> Url: https://github.com/boostorg/json/pull/819#issuecomment-1436791053  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/819/pullrequest-condensed-ee20d45.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/819/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/819/pullrequest.html)

---

## Comment 15

> Username: cppalliance-bot  
> Created_at: 2023-02-20 11:41:49 UTC  
> Url: https://github.com/boostorg/json/pull/819#issuecomment-1436828705  

An automated preview of the documentation is available at [https://819.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html](https://819.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html)

---

## Comment 16

> Username: cppalliance-bot  
> Created_at: 2023-02-20 12:49:44 UTC  
> Url: https://github.com/boostorg/json/pull/819#issuecomment-1436961675  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/819/pullrequest-condensed-1412a7c.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/819/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/819/pullrequest.html)

---

## Comment 17

> Username: cppalliance-bot  
> Created_at: 2023-02-20 12:56:51 UTC  
> Url: https://github.com/boostorg/json/pull/819#issuecomment-1436977001  

An automated preview of the documentation is available at [https://819.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html](https://819.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html)

---

## Comment 18

> Username: cppalliance-bot  
> Created_at: 2023-02-20 13:26:50 UTC  
> Url: https://github.com/boostorg/json/pull/819#issuecomment-1437025320  

An automated preview of the documentation is available at [https://819.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html](https://819.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html)

---

## Comment 19

> Username: cppalliance-bot  
> Created_at: 2023-02-20 14:34:47 UTC  
> Url: https://github.com/boostorg/json/pull/819#issuecomment-1437119557  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/819/pullrequest-condensed-8794bcc.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/819/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/819/pullrequest.html)

---

## Comment 20

> Username: cppalliance-bot  
> Created_at: 2023-02-20 14:51:55 UTC  
> Url: https://github.com/boostorg/json/pull/819#issuecomment-1437142111  

An automated preview of the documentation is available at [https://819.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html](https://819.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html)

---

## Comment 21

> Username: cppalliance-bot  
> Created_at: 2023-02-20 15:11:54 UTC  
> Url: https://github.com/boostorg/json/pull/819#issuecomment-1437171574  

An automated preview of the documentation is available at [https://819.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html](https://819.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html)

---

## Comment 22

> Username: cppalliance-bot  
> Created_at: 2023-02-20 16:14:49 UTC  
> Url: https://github.com/boostorg/json/pull/819#issuecomment-1437261409  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/819/pullrequest-condensed-e0dad98.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/819/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/819/pullrequest.html)

---

## Comment 23

> Username: cppalliance-bot  
> Created_at: 2023-02-20 20:21:53 UTC  
> Url: https://github.com/boostorg/json/pull/819#issuecomment-1437511299  

An automated preview of the documentation is available at [https://819.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html](https://819.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html)

---

## Comment 24

> Username: cppalliance-bot  
> Created_at: 2023-02-20 20:56:53 UTC  
> Url: https://github.com/boostorg/json/pull/819#issuecomment-1437534977  

An automated preview of the documentation is available at [https://819.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html](https://819.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html)

---

## Comment 25

> Username: cppalliance-bot  
> Created_at: 2023-02-20 21:06:51 UTC  
> Url: https://github.com/boostorg/json/pull/819#issuecomment-1437543634  

An automated preview of the documentation is available at [https://819.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html](https://819.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html)

---

## Comment 26

> Username: cppalliance-bot  
> Created_at: 2023-02-20 22:14:49 UTC  
> Url: https://github.com/boostorg/json/pull/819#issuecomment-1437607973  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/819/pullrequest-condensed-d8e0c1c.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/819/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/819/pullrequest.html)

---

## Comment 27

> Username: cppalliance-bot  
> Created_at: 2023-02-21 08:36:54 UTC  
> Url: https://github.com/boostorg/json/pull/819#issuecomment-1438048748  

An automated preview of the documentation is available at [https://819.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html](https://819.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html)

---

## Comment 28

> Username: cppalliance-bot  
> Created_at: 2023-02-21 09:44:49 UTC  
> Url: https://github.com/boostorg/json/pull/819#issuecomment-1438165735  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/819/pullrequest-condensed-65b1a12.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/819/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/819/pullrequest.html)

---

## Comment 29

> Username: cppalliance-bot  
> Created_at: 2023-02-21 10:56:48 UTC  
> Url: https://github.com/boostorg/json/pull/819#issuecomment-1438273498  

An automated preview of the documentation is available at [https://819.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html](https://819.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html)

---

## Comment 30

> Username: codecov[bot]  
> Created_at: 2023-02-21 11:08:20 UTC  
> Updated_at: 2023-06-01 19:13:39 UTC  
> Url: https://github.com/boostorg/json/pull/819#issuecomment-1438291540  

## [Codecov](https://app.codecov.io/gh/boostorg/json/pull/819?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#819](https://app.codecov.io/gh/boostorg/json/pull/819?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (0d545ed) into [develop](https://app.codecov.io/gh/boostorg/json/commit/6a12c322181e01b78fa4003a00cdb96582a81111?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (6a12c32) will **increase** coverage by `0.00%`.  
> The diff coverage is `100.00%`.  
  
> :exclamation: Current head 0d545ed differs from pull request most recent head b9e8b2e. Consider uploading reports for the commit b9e8b2e to get more accurate results  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/json/pull/819/graphs/tree.svg?width=650&height=150&src=pr&token=HNiMmIjyKi&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/json/pull/819?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@           Coverage Diff            @@  
##           develop     #819   +/-   ##  
========================================  
  Coverage    99.24%   99.24%             
========================================  
  Files           71       71             
  Lines         6889     6914   +25       
========================================  
+ Hits          6837     6862   +25       
  Misses          52       52             
```  
  
  
| [Impacted Files](https://app.codecov.io/gh/boostorg/json/pull/819?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/json/detail/value\_from.hpp](https://app.codecov.io/gh/boostorg/json/pull/819?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9qc29uL2RldGFpbC92YWx1ZV9mcm9tLmhwcA==) | `100.00% <100.00%> (ø)` | |  
| [include/boost/json/detail/value\_to.hpp](https://app.codecov.io/gh/boostorg/json/pull/819?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9qc29uL2RldGFpbC92YWx1ZV90by5ocHA=) | `100.00% <100.00%> (ø)` | |  
| [include/boost/json/impl/conversion.hpp](https://app.codecov.io/gh/boostorg/json/pull/819?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9qc29uL2ltcGwvY29udmVyc2lvbi5ocHA=) | `100.00% <100.00%> (ø)` | |  
| [include/boost/json/value\_from.hpp](https://app.codecov.io/gh/boostorg/json/pull/819?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9qc29uL3ZhbHVlX2Zyb20uaHBw) | `100.00% <100.00%> (ø)` | |  
| [include/boost/json/value\_to.hpp](https://app.codecov.io/gh/boostorg/json/pull/819?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9qc29uL3ZhbHVlX3RvLmhwcA==) | `100.00% <100.00%> (ø)` | |  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/json/pull/819?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/json/pull/819?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [6a12c32...b9e8b2e](https://app.codecov.io/gh/boostorg/json/pull/819?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---

## Comment 31

> Username: cppalliance-bot  
> Created_at: 2023-02-21 12:04:46 UTC  
> Url: https://github.com/boostorg/json/pull/819#issuecomment-1438364781  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/819/pullrequest-condensed-a9efe69.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/819/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/819/pullrequest.html)

---

## Comment 32

> Username: cppalliance-bot  
> Created_at: 2023-02-21 13:01:51 UTC  
> Url: https://github.com/boostorg/json/pull/819#issuecomment-1438445110  

An automated preview of the documentation is available at [https://819.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html](https://819.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html)

---

## Comment 33

> Username: cppalliance-bot  
> Created_at: 2023-02-21 13:21:52 UTC  
> Url: https://github.com/boostorg/json/pull/819#issuecomment-1438483251  

An automated preview of the documentation is available at [https://819.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html](https://819.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html)

---

## Comment 34

> Username: cppalliance-bot  
> Created_at: 2023-02-21 14:24:43 UTC  
> Url: https://github.com/boostorg/json/pull/819#issuecomment-1438580423  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/819/pullrequest-condensed-aa51b3b.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/819/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/819/pullrequest.html)

---

## Comment 35

> Username: cppalliance-bot  
> Created_at: 2023-02-23 06:06:52 UTC  
> Url: https://github.com/boostorg/json/pull/819#issuecomment-1441254591  

An automated preview of the documentation is available at [https://819.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html](https://819.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html)

---

## Comment 36

> Username: cppalliance-bot  
> Created_at: 2023-02-23 07:14:48 UTC  
> Url: https://github.com/boostorg/json/pull/819#issuecomment-1441297718  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/819/pullrequest-condensed-ed792f9.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/819/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/819/pullrequest.html)

---

## Comment 37

> Username: cppalliance-bot  
> Created_at: 2023-02-23 14:46:54 UTC  
> Url: https://github.com/boostorg/json/pull/819#issuecomment-1441916739  

An automated preview of the documentation is available at [https://819.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html](https://819.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html)

---

## Comment 38

> Username: cppalliance-bot  
> Created_at: 2023-02-23 15:11:52 UTC  
> Url: https://github.com/boostorg/json/pull/819#issuecomment-1441954470  

An automated preview of the documentation is available at [https://819.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html](https://819.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html)

---

## Comment 39

> Username: cppalliance-bot  
> Created_at: 2023-02-23 15:21:50 UTC  
> Url: https://github.com/boostorg/json/pull/819#issuecomment-1441969934  

An automated preview of the documentation is available at [https://819.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html](https://819.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html)

---

## Comment 40

> Username: cppalliance-bot  
> Created_at: 2023-02-23 16:29:46 UTC  
> Url: https://github.com/boostorg/json/pull/819#issuecomment-1442075401  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/819/pullrequest-condensed-e44408a.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/819/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/819/pullrequest.html)

---

## Comment 41

> Username: cppalliance-bot  
> Created_at: 2023-02-23 18:51:52 UTC  
> Url: https://github.com/boostorg/json/pull/819#issuecomment-1442269280  

An automated preview of the documentation is available at [https://819.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html](https://819.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html)

---

## Comment 42

> Username: cppalliance-bot  
> Created_at: 2023-02-23 19:54:48 UTC  
> Url: https://github.com/boostorg/json/pull/819#issuecomment-1442351864  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/819/pullrequest-condensed-fb9c168.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/819/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/819/pullrequest.html)

---

## Comment 43

> Username: cppalliance-bot  
> Created_at: 2023-02-24 13:56:54 UTC  
> Url: https://github.com/boostorg/json/pull/819#issuecomment-1443714107  

An automated preview of the documentation is available at [https://819.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html](https://819.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html)

---

## Comment 44

> Username: cppalliance-bot  
> Created_at: 2023-02-24 15:04:53 UTC  
> Url: https://github.com/boostorg/json/pull/819#issuecomment-1443814836  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/819/pullrequest-condensed-c10ff70.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/819/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/819/pullrequest.html)

---

## Comment 45

> Username: cppalliance-bot  
> Created_at: 2023-02-24 15:11:54 UTC  
> Url: https://github.com/boostorg/json/pull/819#issuecomment-1443825772  

An automated preview of the documentation is available at [https://819.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html](https://819.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html)

---

## Comment 46

> Username: cppalliance-bot  
> Created_at: 2023-02-24 15:46:54 UTC  
> Url: https://github.com/boostorg/json/pull/819#issuecomment-1443875158  

An automated preview of the documentation is available at [https://819.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html](https://819.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html)

---

## Comment 47

> Username: cppalliance-bot  
> Created_at: 2023-02-24 16:54:45 UTC  
> Url: https://github.com/boostorg/json/pull/819#issuecomment-1444011979  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/819/pullrequest-condensed-f6151c3.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/819/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/819/pullrequest.html)

---

## Comment 48

> Username: cppalliance-bot  
> Created_at: 2023-02-24 17:56:53 UTC  
> Url: https://github.com/boostorg/json/pull/819#issuecomment-1444142846  

An automated preview of the documentation is available at [https://819.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html](https://819.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html)

---

## Comment 49

> Username: cppalliance-bot  
> Created_at: 2023-02-24 19:04:49 UTC  
> Url: https://github.com/boostorg/json/pull/819#issuecomment-1444277587  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/819/pullrequest-condensed-8e24a04.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/819/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/819/pullrequest.html)

---

## Comment 50

> Username: cppalliance-bot  
> Created_at: 2023-02-24 19:26:54 UTC  
> Url: https://github.com/boostorg/json/pull/819#issuecomment-1444315780  

An automated preview of the documentation is available at [https://819.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html](https://819.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html)

---

## Comment 51

> Username: cppalliance-bot  
> Created_at: 2023-02-24 19:36:52 UTC  
> Url: https://github.com/boostorg/json/pull/819#issuecomment-1444333108  

An automated preview of the documentation is available at [https://819.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html](https://819.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html)

---

## Comment 52

> Username: cppalliance-bot  
> Created_at: 2023-02-24 20:44:48 UTC  
> Url: https://github.com/boostorg/json/pull/819#issuecomment-1444452130  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/819/pullrequest-condensed-3c77352.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/819/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/819/pullrequest.html)

---

## Comment 53

> Username: cppalliance-bot  
> Created_at: 2023-02-25 14:06:46 UTC  
> Url: https://github.com/boostorg/json/pull/819#issuecomment-1445127500  

An automated preview of the documentation is available at [https://819.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html](https://819.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html)

---

## Comment 54

> Username: cppalliance-bot  
> Created_at: 2023-02-25 15:14:48 UTC  
> Url: https://github.com/boostorg/json/pull/819#issuecomment-1445139956  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/819/pullrequest-condensed-c6748a9.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/819/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/819/pullrequest.html)

---

## Comment 55

> Username: cppalliance-bot  
> Created_at: 2023-02-27 08:56:49 UTC  
> Url: https://github.com/boostorg/json/pull/819#issuecomment-1445936498  

An automated preview of the documentation is available at [https://819.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html](https://819.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html)

---

## Comment 56

> Username: cppalliance-bot  
> Created_at: 2023-02-27 10:04:48 UTC  
> Url: https://github.com/boostorg/json/pull/819#issuecomment-1446041231  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/819/pullrequest-condensed-9ee63d9.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/819/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/819/pullrequest.html)

---

## Comment 57

> Username: cppalliance-bot  
> Created_at: 2023-03-01 09:21:50 UTC  
> Url: https://github.com/boostorg/json/pull/819#issuecomment-1449668170  

An automated preview of the documentation is available at [https://819.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html](https://819.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html)

---

## Comment 58

> Username: cppalliance-bot  
> Created_at: 2023-03-01 10:29:54 UTC  
> Url: https://github.com/boostorg/json/pull/819#issuecomment-1449817645  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/819/pullrequest-condensed-5d9146a.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/819/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/819/pullrequest.html)

---

## Comment 59

> Username: cppalliance-bot  
> Created_at: 2023-06-01 17:31:48 UTC  
> Url: https://github.com/boostorg/json/pull/819#issuecomment-1572505254  

An automated preview of the documentation is available at [https://819.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html](https://819.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html)

---

## Comment 60

> Username: cppalliance-bot  
> Created_at: 2023-06-01 18:51:43 UTC  
> Url: https://github.com/boostorg/json/pull/819#issuecomment-1572605555  

An automated preview of the documentation is available at [https://819.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html](https://819.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html)

---

## Comment 61

> Username: cppalliance-bot  
> Created_at: 2023-06-01 20:00:36 UTC  
> Url: https://github.com/boostorg/json/pull/819#issuecomment-1572692407  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/819/pullrequest-condensed-d0a1173.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/819/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/819/pullrequest.html)

---
