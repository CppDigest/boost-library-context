# #1070 Boost Intrusive to be able to print the json pointer of where an error occourred when performing the value_to operation. [Open]

> Username: RoyBellingan  
> Created at: 2025-02-11 17:26:32 UTC  
> Updated at: 2025-12-23 17:43:22 UTC  
> Url: https://github.com/boostorg/json/pull/1070  

When used with  
  
https://github.com/RoyBellingan/boostIntrusive/tree/master  
  
It should produce this  
  
```  
  
  
------  
default msg : source composite size does not match target size  
expanded msg: the key >> timestamp << is non optional and missing in path /0/comment/0  
  
------  
default msg : source composite size does not match target size  
expanded msg: the key >> author << is non optional and missing in path /0/comment/0/likes/1  
  
------  
default msg : not a number  
expanded msg: Last json pointer was /0/comment/0/timestamp  
  
```

---

## Comment 1

> Username: cppalliance-bot  
> Created_at: 2025-02-11 17:33:28 UTC  
> Url: https://github.com/boostorg/json/pull/1070#issuecomment-2651549768  

An automated preview of the documentation is available at [https://1070.json.prtest2.cppalliance.org/libs/json/doc/html/index.html](https://1070.json.prtest2.cppalliance.org/libs/json/doc/html/index.html)

---

## Comment 2

> Username: cppalliance-bot  
> Created_at: 2025-02-15 15:28:26 UTC  
> Url: https://github.com/boostorg/json/pull/1070#issuecomment-2660971608  

An automated preview of the documentation is available at [https://1070.json.prtest2.cppalliance.org/libs/json/doc/html/index.html](https://1070.json.prtest2.cppalliance.org/libs/json/doc/html/index.html)

---

## Comment 3

> Username: cppalliance-bot  
> Created_at: 2025-02-18 01:08:23 UTC  
> Url: https://github.com/boostorg/json/pull/1070#issuecomment-2664330523  

An automated preview of the documentation is available at [https://1070.json.prtest2.cppalliance.org/libs/json/doc/html/index.html](https://1070.json.prtest2.cppalliance.org/libs/json/doc/html/index.html)

---

## Comment 4

> Username: cppalliance-bot  
> Created_at: 2025-02-18 01:14:38 UTC  
> Url: https://github.com/boostorg/json/pull/1070#issuecomment-2664336060  

An automated preview of the documentation is available at [https://1070.json.prtest2.cppalliance.org/libs/json/doc/html/index.html](https://1070.json.prtest2.cppalliance.org/libs/json/doc/html/index.html)

---

## Comment 5

> Username: codecov[bot]  
> Created_at: 2025-02-18 01:51:33 UTC  
> Updated_at: 2025-02-18 02:06:03 UTC  
> Url: https://github.com/boostorg/json/pull/1070#issuecomment-2664428703  

## [Codecov](https://app.codecov.io/gh/boostorg/json/pull/1070?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
All modified and coverable lines are covered by tests :white_check_mark:  
> Project coverage is 93.70%. Comparing base [(`c02d872`)](https://app.codecov.io/gh/boostorg/json/commit/c02d8721efeb3c4f7b9d86758a48e11edb523ae5?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) to head [(`220ea73`)](https://app.codecov.io/gh/boostorg/json/commit/220ea7325af4d9c546199b046baa4303837a236d?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/json/pull/1070/graphs/tree.svg?width=650&height=150&src=pr&token=HNiMmIjyKi&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/json/pull/1070?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@           Coverage Diff            @@  
##           develop    #1070   +/-   ##  
========================================  
  Coverage    93.70%   93.70%             
========================================  
  Files           91       91             
  Lines         9139     9139             
========================================  
  Hits          8564     8564             
  Misses         575      575             
```  
  
| [Files with missing lines](https://app.codecov.io/gh/boostorg/json/pull/1070?dropdown=coverage&src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/json/detail/value\_to.hpp](https://app.codecov.io/gh/boostorg/json/pull/1070?src=pr&el=tree&filepath=include%2Fboost%2Fjson%2Fdetail%2Fvalue_to.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9qc29uL2RldGFpbC92YWx1ZV90by5ocHA=) | `100.00% <100.00%> (ø)` | |  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/json/pull/1070?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/json/pull/1070?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [c02d872...220ea73](https://app.codecov.io/gh/boostorg/json/pull/1070?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---

## Comment 6

> Username: cppalliance-bot  
> Created_at: 2025-02-18 02:13:00 UTC  
> Url: https://github.com/boostorg/json/pull/1070#issuecomment-2664450687  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/1070/pullrequest-condensed-e3b13b1.png)  
 Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/1070/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/1070/pullrequest.html)

---

## Comment 7

> Username: cppalliance-bot  
> Created_at: 2025-02-18 03:23:39 UTC  
> Url: https://github.com/boostorg/json/pull/1070#issuecomment-2664519204  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/1070/pullrequest-condensed-c5c68fb.png)  
 Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/1070/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/1070/pullrequest.html)

---

## Comment 8

> Username: grisumbras  
> Created_at: 2025-02-19 10:36:48 UTC  
> Url: https://github.com/boostorg/json/pull/1070#issuecomment-2668231565  

I have an idea for an alternative approach. I'll implement it first, so that I can compare yours with mine.

---

## Comment 9

> Username: grisumbras  
> Created_at: 2025-02-25 02:13:37 UTC  
> Url: https://github.com/boostorg/json/pull/1070#issuecomment-2680208181  

My alternative: #1075.

---

## Comment 10

> Username: cppalliance-bot  
> Created_at: 2025-12-23 17:43:22 UTC  
> Url: https://github.com/boostorg/json/pull/1070#issuecomment-3687459988  

GCOVR code coverage report [https://1070.json.prtest2.cppalliance.org/gcovr/index.html](https://1070.json.prtest2.cppalliance.org/gcovr/index.html)  
LCOV code coverage report [https://1070.json.prtest2.cppalliance.org/genhtml/index.html](https://1070.json.prtest2.cppalliance.org/genhtml/index.html)  
Coverage Diff [https://1070.json.prtest2.cppalliance.org/gcovr/coverage_diff.txt](https://1070.json.prtest2.cppalliance.org/gcovr/coverage_diff.txt)  
  
Build time: 2025-12-23 17:43:21 UTC

---
