# #943 refactor parse_unsigned [Open]

> Username: grisumbras  
> Created at: 2023-09-24 14:21:59 UTC  
> Updated at: 2024-03-25 20:39:01 UTC  
> Url: https://github.com/boostorg/json/pull/943  



---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2023-09-24 14:41:54 UTC  
> Updated_at: 2024-03-13 12:13:14 UTC  
> Url: https://github.com/boostorg/json/pull/943#issuecomment-1732588168  

## [Codecov](https://app.codecov.io/gh/boostorg/json/pull/943?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
All modified and coverable lines are covered by tests :white_check_mark:  
> Project coverage is 93.08%. Comparing base [(`2883ff1`)](https://app.codecov.io/gh/boostorg/json/commit/2883ff1689727f77157a7a9a603d9b8f23a5cb53?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) to head [(`31a853e`)](https://app.codecov.io/gh/boostorg/json/pull/943?dropdown=coverage&src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/json/pull/943/graphs/tree.svg?width=650&height=150&src=pr&token=HNiMmIjyKi&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/json/pull/943?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #943      +/-   ##  
===========================================  
- Coverage    93.08%   93.08%   -0.01%       
===========================================  
  Files           87       87                
  Lines         8125     8122       -3       
===========================================  
- Hits          7563     7560       -3       
  Misses         562      562                
```  
  
  
| [Files](https://app.codecov.io/gh/boostorg/json/pull/943?dropdown=coverage&src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/json/detail/sse2.hpp](https://app.codecov.io/gh/boostorg/json/pull/943?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9qc29uL2RldGFpbC9zc2UyLmhwcA==) | `97.76% <100.00%> (-0.05%)` | :arrow_down: |  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/json/pull/943?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/json/pull/943?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [2883ff1...31a853e](https://app.codecov.io/gh/boostorg/json/pull/943?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---

## Comment 2

> Username: cppalliance-bot  
> Created_at: 2023-09-24 15:35:48 UTC  
> Url: https://github.com/boostorg/json/pull/943#issuecomment-1732598750  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/943/pullrequest-condensed-bb1e561.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/943/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/943/pullrequest.html)

---

## Comment 3

> Username: cppalliance-bot  
> Created_at: 2023-09-24 17:00:47 UTC  
> Url: https://github.com/boostorg/json/pull/943#issuecomment-1732620293  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/943/pullrequest-condensed-2121b87.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/943/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/943/pullrequest.html)

---

## Comment 4

> Username: cppalliance-bot  
> Created_at: 2023-09-29 10:45:44 UTC  
> Url: https://github.com/boostorg/json/pull/943#issuecomment-1740690600  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/943/pullrequest-condensed-9bfa47c.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/943/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/943/pullrequest.html)

---

## Comment 5

> Username: cppalliance-bot  
> Created_at: 2023-09-29 19:10:20 UTC  
> Url: https://github.com/boostorg/json/pull/943#issuecomment-1741366014  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/943/pullrequest-condensed-a3284cf.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/943/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/943/pullrequest.html)

---

## Comment 6

> Username: cppalliance-bot  
> Created_at: 2023-11-15 14:51:14 UTC  
> Url: https://github.com/boostorg/json/pull/943#issuecomment-1812681260  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/943/pullrequest-condensed-6314768.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/943/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/943/pullrequest.html)

---

## Comment 7

> Username: cppalliance-bot  
> Created_at: 2024-02-19 22:52:13 UTC  
> Url: https://github.com/boostorg/json/pull/943#issuecomment-1953247601  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/943/pullrequest-condensed-b208833.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/943/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/943/pullrequest.html)

---

## Comment 8

> Username: cppalliance-bot  
> Created_at: 2024-03-13 12:52:15 UTC  
> Url: https://github.com/boostorg/json/pull/943#issuecomment-1994335796  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/943/pullrequest-condensed-1cc9696.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/943/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/943/pullrequest.html)

---

## Comment 9

> Username: cppalliance-bot  
> Created_at: 2024-03-25 16:57:19 UTC  
> Url: https://github.com/boostorg/json/pull/943#issuecomment-2018471964  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/943/pullrequest-condensed-052d900.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/943/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/943/pullrequest.html)

---

## Comment 10

> Username: cppalliance-bot  
> Created_at: 2024-03-25 20:39:00 UTC  
> Url: https://github.com/boostorg/json/pull/943#issuecomment-2018873786  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/943/pullrequest-condensed-8e2d7fd.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/943/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/943/pullrequest.html)

---
