# #915 Use Boost.Endian for endianness [Merged]

> Username: grisumbras  
> Created at: 2023-07-12 14:19:19 UTC  
> Updated at: 2024-02-13 17:49:43 UTC  
> Merged at: 2024-02-13 10:04:58 UTC  
> Closed at: 2024-02-13 10:04:58 UTC  
> Url: https://github.com/boostorg/json/pull/915  



---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2023-07-12 14:39:14 UTC  
> Updated_at: 2024-02-13 06:56:24 UTC  
> Url: https://github.com/boostorg/json/pull/915#issuecomment-1632652518  

## [Codecov](https://app.codecov.io/gh/boostorg/json/pull/915?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
All modified and coverable lines are covered by tests :white_check_mark:  
> Comparison is base [(`bacc644`)](https://app.codecov.io/gh/boostorg/json/commit/bacc644f93c9b2000cd137ebf541ad754d6ca747?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) 93.08% compared to head [(`5663210`)](https://app.codecov.io/gh/boostorg/json/pull/915?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) 93.08%.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/json/pull/915/graphs/tree.svg?width=650&height=150&src=pr&token=HNiMmIjyKi&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/json/pull/915?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@           Coverage Diff            @@  
##           develop     #915   +/-   ##  
========================================  
  Coverage    93.08%   93.08%             
========================================  
  Files           87       87             
  Lines         8122     8125    +3       
========================================  
+ Hits          7560     7563    +3       
  Misses         562      562             
```  
  
  
| [Files](https://app.codecov.io/gh/boostorg/json/pull/915?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [...detail/charconv/detail/fast\_float/ascii\_number.hpp](https://app.codecov.io/gh/boostorg/json/pull/915?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9qc29uL2RldGFpbC9jaGFyY29udi9kZXRhaWwvZmFzdF9mbG9hdC9hc2NpaV9udW1iZXIuaHBw) | `92.07% <100.00%> (+0.07%)` | :arrow_up: |  
| [include/boost/json/detail/config.hpp](https://app.codecov.io/gh/boostorg/json/pull/915?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9qc29uL2RldGFpbC9jb25maWcuaHBw) | `100.00% <ø> (ø)` | |  
| [include/boost/json/detail/sse2.hpp](https://app.codecov.io/gh/boostorg/json/pull/915?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9qc29uL2RldGFpbC9zc2UyLmhwcA==) | `97.81% <100.00%> (+0.01%)` | :arrow_up: |  
| [include/boost/json/detail/utf8.hpp](https://app.codecov.io/gh/boostorg/json/pull/915?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9qc29uL2RldGFpbC91dGY4LmhwcA==) | `100.00% <100.00%> (ø)` | |  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/json/pull/915?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/json/pull/915?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [bacc644...5663210](https://app.codecov.io/gh/boostorg/json/pull/915?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---

## Comment 2

> Username: cppalliance-bot  
> Created_at: 2023-07-12 15:35:49 UTC  
> Url: https://github.com/boostorg/json/pull/915#issuecomment-1632767136  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/915/pullrequest-condensed-b455330.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/915/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/915/pullrequest.html)

---

## Comment 3

> Username: cppalliance-bot  
> Created_at: 2023-07-14 12:40:49 UTC  
> Url: https://github.com/boostorg/json/pull/915#issuecomment-1635807380  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/915/pullrequest-condensed-0f20514.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/915/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/915/pullrequest.html)

---

## Comment 4

> Username: cppalliance-bot  
> Created_at: 2023-07-14 15:18:15 UTC  
> Url: https://github.com/boostorg/json/pull/915#issuecomment-1636012672  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/915/pullrequest-condensed-8b5952b.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/915/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/915/pullrequest.html)

---

## Comment 5

> Username: vinniefalco  
> Created_at: 2023-10-27 18:03:35 UTC  
> Url: https://github.com/boostorg/json/pull/915#issuecomment-1783307252  

Refresh my memory - what does JSON have to do with endianness at all?

---

## Comment 6

> Username: grisumbras  
> Created_at: 2023-10-27 18:06:31 UTC  
> Url: https://github.com/boostorg/json/pull/915#issuecomment-1783310495  

At several points we load buffers into numbers for bitwise comparison. This seems to have been done for optimisation puproses

---

## Comment 7

> Username: cppalliance-bot  
> Created_at: 2023-10-27 19:15:23 UTC  
> Url: https://github.com/boostorg/json/pull/915#issuecomment-1783390398  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/915/pullrequest-condensed-b309e8f.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/915/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/915/pullrequest.html)

---

## Comment 8

> Username: cppalliance-bot  
> Created_at: 2023-11-14 11:34:12 UTC  
> Url: https://github.com/boostorg/json/pull/915#issuecomment-1810037453  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/915/pullrequest-condensed-56c7836.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/915/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/915/pullrequest.html)

---

## Comment 9

> Username: cppalliance-bot  
> Created_at: 2023-11-20 15:56:14 UTC  
> Url: https://github.com/boostorg/json/pull/915#issuecomment-1819336320  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/915/pullrequest-condensed-95080e3.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/915/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/915/pullrequest.html)

---

## Comment 10

> Username: cppalliance-bot  
> Created_at: 2024-02-09 13:22:12 UTC  
> Url: https://github.com/boostorg/json/pull/915#issuecomment-1935919795  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/915/pullrequest-condensed-8c52cb3.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/915/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/915/pullrequest.html)

---

## Comment 11

> Username: cppalliance-bot  
> Created_at: 2024-02-09 14:37:10 UTC  
> Url: https://github.com/boostorg/json/pull/915#issuecomment-1936046906  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/915/pullrequest-condensed-ca86581.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/915/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/915/pullrequest.html)

---

## Comment 12

> Username: cppalliance-bot  
> Created_at: 2024-02-12 21:07:10 UTC  
> Url: https://github.com/boostorg/json/pull/915#issuecomment-1939583173  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/915/pullrequest-condensed-edafdf3.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/915/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/915/pullrequest.html)

---

## Comment 13

> Username: pdimov  
> Created_at: 2024-02-13 17:49:42 UTC  
> Url: https://github.com/boostorg/json/pull/915#issuecomment-1942095984  

Instead of reversing in place, you can just use the Endian load/store functions.

---
