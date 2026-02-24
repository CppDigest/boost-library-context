# #1144 use curly brace construction of tuples [Merged]

> Username: grisumbras  
> Created at: 2026-02-13 12:45:43 UTC  
> Updated at: 2026-02-18 19:18:26 UTC  
> Merged at: 2026-02-18 16:47:46 UTC  
> Closed at: 2026-02-18 16:47:46 UTC  
> Url: https://github.com/boostorg/json/pull/1144  

Fix #1140.

---

## Comment 1

> Username: cppalliance-bot  
> Created_at: 2026-02-13 12:55:37 UTC  
> Updated_at: 2026-02-18 15:49:33 UTC  
> Url: https://github.com/boostorg/json/pull/1144#issuecomment-3897099385  

An automated preview of the documentation is available at [https://1144.json.prtest2.cppalliance.org/libs/json/doc/html/index.html](https://1144.json.prtest2.cppalliance.org/libs/json/doc/html/index.html)  
  
If more commits are pushed to the pull request, the docs will rebuild at the same URL.  
  
2026-02-18 15:49:32 UTC

---

## Comment 2

> Username: cppalliance-bot  
> Created_at: 2026-02-13 13:12:37 UTC  
> Updated_at: 2026-02-18 16:11:29 UTC  
> Url: https://github.com/boostorg/json/pull/1144#issuecomment-3897178702  

GCOVR code coverage report [https://1144.json.prtest2.cppalliance.org/gcovr/index.html](https://1144.json.prtest2.cppalliance.org/gcovr/index.html)  
LCOV code coverage report [https://1144.json.prtest2.cppalliance.org/genhtml/index.html](https://1144.json.prtest2.cppalliance.org/genhtml/index.html)  
Coverage Diff Report [https://1144.json.prtest2.cppalliance.org/diff-report/index.html](https://1144.json.prtest2.cppalliance.org/diff-report/index.html)  
  
Build time: 2026-02-18 16:11:27 UTC

---

## Comment 3

> Username: cppalliance-bot  
> Created_at: 2026-02-13 13:58:06 UTC  
> Url: https://github.com/boostorg/json/pull/1144#issuecomment-3897389461  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/1144/pullrequest-condensed-a3b7c71.png)  
 Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/1144/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/1144/pullrequest.html)

---

## Comment 4

> Username: cppalliance-bot  
> Created_at: 2026-02-13 18:05:43 UTC  
> Url: https://github.com/boostorg/json/pull/1144#issuecomment-3898579122  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/1144/pullrequest-condensed-acf2817.png)  
 Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/1144/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/1144/pullrequest.html)

---

## Comment 5

> Username: cppalliance-bot  
> Created_at: 2026-02-18 13:18:07 UTC  
> Url: https://github.com/boostorg/json/pull/1144#issuecomment-3920789669  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/1144/pullrequest-condensed-257efa0.png)  
 Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/1144/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/1144/pullrequest.html)

---

## Comment 6

> Username: cppalliance-bot  
> Created_at: 2026-02-18 14:47:55 UTC  
> Url: https://github.com/boostorg/json/pull/1144#issuecomment-3921253185  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/1144/pullrequest-condensed-654a4d9.png)  
 Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/1144/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/1144/pullrequest.html)

---

## Comment 7

> Username: codecov[bot]  
> Created_at: 2026-02-18 16:34:38 UTC  
> Updated_at: 2026-02-18 19:18:26 UTC  
> Url: https://github.com/boostorg/json/pull/1144#issuecomment-3921847096  

## [Codecov](https://app.codecov.io/gh/boostorg/json/pull/1144?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
:white_check_mark: All modified and coverable lines are covered by tests.  
:white_check_mark: Project coverage is 93.67%. Comparing base ([`6238e35`](https://app.codecov.io/gh/boostorg/json/commit/6238e35f8dc272a3b3eb426a919136cb782ccb10?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)) to head ([`4df9a86`](https://app.codecov.io/gh/boostorg/json/commit/4df9a86a206beac4ac12ec8ac16a1561e83acacf?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)).  
:warning: Report is 1 commits behind head on develop.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/json/pull/1144/graphs/tree.svg?width=650&height=150&src=pr&token=HNiMmIjyKi&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/json/pull/1144?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@           Coverage Diff            @@  
##           develop    #1144   +/-   ##  
========================================  
  Coverage    93.67%   93.67%             
========================================  
  Files           91       91             
  Lines         9153     9154    +1       
========================================  
+ Hits          8574     8575    +1       
  Misses         579      579             
```  
  
| [Files with missing lines](https://app.codecov.io/gh/boostorg/json/pull/1144?dropdown=coverage&src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/json/detail/value\_to.hpp](https://app.codecov.io/gh/boostorg/json/pull/1144?src=pr&el=tree&filepath=include%2Fboost%2Fjson%2Fdetail%2Fvalue_to.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9qc29uL2RldGFpbC92YWx1ZV90by5ocHA=) | `100.00% <100.00%> (ø)` | |  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/json/pull/1144?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/json/pull/1144?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [6238e35...4df9a86](https://app.codecov.io/gh/boostorg/json/pull/1144?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
</details>  
<details><summary> :rocket: New features to boost your workflow: </summary>  
  
- :snowflake: [Test Analytics](https://docs.codecov.com/docs/test-analytics): Detect flaky tests, report on failures, and find test suite problems.  
</details>

---

## Comment 8

> Username: cppalliance-bot  
> Created_at: 2026-02-18 16:47:52 UTC  
> Url: https://github.com/boostorg/json/pull/1144#issuecomment-3921915415  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/1144/pullrequest-condensed-173f03a.png)  
 Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/1144/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/1144/pullrequest.html)

---
