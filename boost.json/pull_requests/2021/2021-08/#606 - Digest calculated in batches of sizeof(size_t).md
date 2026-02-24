# #606 Digest calculated in batches of sizeof(size_t) [Closed]

> Username: grisumbras  
> Created at: 2021-08-08 14:24:11 UTC  
> Updated at: 2025-08-12 10:32:20 UTC  
> Closed at: 2025-08-12 10:32:11 UTC  
> Url: https://github.com/boostorg/json/pull/606  

Fixes #601

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2021-08-08 14:29:01 UTC  
> Updated_at: 2025-08-12 10:32:20 UTC  
> Url: https://github.com/boostorg/json/pull/606#issuecomment-894806175  

## [Codecov](https://app.codecov.io/gh/boostorg/json/pull/606?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
:white_check_mark: All modified and coverable lines are covered by tests.  
:white_check_mark: Project coverage is 93.71%. Comparing base ([`3c80c09`](https://app.codecov.io/gh/boostorg/json/commit/3c80c097d2fe2266d3d41f4121d7af24e122e996?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)) to head ([`55cc345`](https://app.codecov.io/gh/boostorg/json/commit/55cc34586da4b2cec95334d2ca992713f586e69c?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)).  
:warning: Report is 1 commits behind head on develop.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/json/pull/606/graphs/tree.svg?width=650&height=150&src=pr&token=HNiMmIjyKi&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/json/pull/606?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@           Coverage Diff            @@  
##           develop     #606   +/-   ##  
========================================  
  Coverage    93.70%   93.71%             
========================================  
  Files           91       91             
  Lines         9136     9149   +13       
========================================  
+ Hits          8561     8574   +13       
  Misses         575      575             
```  
  
| [Files with missing lines](https://app.codecov.io/gh/boostorg/json/pull/606?dropdown=coverage&src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/json/detail/digest.hpp](https://app.codecov.io/gh/boostorg/json/pull/606?src=pr&el=tree&filepath=include%2Fboost%2Fjson%2Fdetail%2Fdigest.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9qc29uL2RldGFpbC9kaWdlc3QuaHBw) | `100.00% <100.00%> (ø)` | |  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/json/pull/606?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/json/pull/606?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [3c80c09...55cc345](https://app.codecov.io/gh/boostorg/json/pull/606?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
</details>  
<details><summary> :rocket: New features to boost your workflow: </summary>  
  
- :snowflake: [Test Analytics](https://docs.codecov.com/docs/test-analytics): Detect flaky tests, report on failures, and find test suite problems.  
</details>

---

## Comment 2

> Username: cppalliance-bot  
> Created_at: 2021-08-08 15:53:58 UTC  
> Url: https://github.com/boostorg/json/pull/606#issuecomment-894817155  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/606/pullrequest-condensed-03e4877.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/606/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/606/pullrequest.html)

---

## Comment 3

> Username: vinniefalco  
> Created_at: 2021-10-05 15:13:21 UTC  
> Url: https://github.com/boostorg/json/pull/606#issuecomment-934500769  

What is the status of this?

---

## Comment 4

> Username: grisumbras  
> Created_at: 2021-10-10 09:30:55 UTC  
> Url: https://github.com/boostorg/json/pull/606#issuecomment-939439292  

I've made 6 variants of this and benched them. Here are the results:  
https://docs.google.com/spreadsheets/d/1WEfTLx5vEb5TduzzHt-OkAj6HnS4ku4Y4Y7C3YGIgc8/edit?usp=sharing  
The 6th approach seems most promising, fairly small regression for a few tests and huge improvements for many tests. But my local benchmarking disagrees with CI as you can see on the picture posted above by the bot.

---

## Comment 5

> Username: vinniefalco  
> Created_at: 2021-10-10 10:24:19 UTC  
> Url: https://github.com/boostorg/json/pull/606#issuecomment-939451525  

yeah the automated performance CI bot is not accurate

---

## Comment 6

> Username: cppalliance-bot  
> Created_at: 2021-10-10 10:34:03 UTC  
> Url: https://github.com/boostorg/json/pull/606#issuecomment-939454132  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/606/pullrequest-condensed-26860ed.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/606/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/606/pullrequest.html)

---

## Comment 7

> Username: vinniefalco  
> Created_at: 2021-10-27 23:41:47 UTC  
> Url: https://github.com/boostorg/json/pull/606#issuecomment-953387896  

FYI this is still on my radar, but I have to wait until I return to boost.http_proto.

---

## Comment 8

> Username: cppalliance-bot  
> Created_at: 2023-06-15 16:55:36 UTC  
> Url: https://github.com/boostorg/json/pull/606#issuecomment-1593418768  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/606/pullrequest-condensed-4f83329.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/606/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/606/pullrequest.html)

---

## Comment 9

> Username: cppalliance-bot  
> Created_at: 2023-06-15 18:25:38 UTC  
> Url: https://github.com/boostorg/json/pull/606#issuecomment-1593538198  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/606/pullrequest-condensed-fdc0315.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/606/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/606/pullrequest.html)

---

## Comment 10

> Username: cppalliance-bot  
> Created_at: 2023-06-15 22:00:38 UTC  
> Url: https://github.com/boostorg/json/pull/606#issuecomment-1593773238  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/606/pullrequest-condensed-b96c3ea.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/606/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/606/pullrequest.html)

---

## Comment 11

> Username: cppalliance-bot  
> Created_at: 2023-06-16 09:20:40 UTC  
> Url: https://github.com/boostorg/json/pull/606#issuecomment-1594384191  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/606/pullrequest-condensed-19d57a2.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/606/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/606/pullrequest.html)

---

## Comment 12

> Username: cppalliance-bot  
> Created_at: 2023-06-16 16:10:38 UTC  
> Url: https://github.com/boostorg/json/pull/606#issuecomment-1594926679  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/606/pullrequest-condensed-1b26887.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/606/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/606/pullrequest.html)

---

## Comment 13

> Username: cppalliance-bot  
> Created_at: 2023-06-19 16:45:42 UTC  
> Url: https://github.com/boostorg/json/pull/606#issuecomment-1597479290  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/606/pullrequest-condensed-f8c7031.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/606/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/606/pullrequest.html)

---

## Comment 14

> Username: cppalliance-bot  
> Created_at: 2025-08-01 13:40:04 UTC  
> Url: https://github.com/boostorg/json/pull/606#issuecomment-3144634025  

An automated preview of the documentation is available at [https://606.json.prtest2.cppalliance.org/libs/json/doc/html/index.html](https://606.json.prtest2.cppalliance.org/libs/json/doc/html/index.html)

---

## Comment 15

> Username: cppalliance-bot  
> Created_at: 2025-08-01 14:41:58 UTC  
> Url: https://github.com/boostorg/json/pull/606#issuecomment-3144823380  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/606/pullrequest-condensed-71df4e6.png)  
 Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/606/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/606/pullrequest.html)

---

## Comment 16

> Username: grisumbras  
> Created_at: 2025-08-12 10:31:58 UTC  
> Url: https://github.com/boostorg/json/pull/606#issuecomment-3178753221  

Abandoned in favour of #1094.

---
