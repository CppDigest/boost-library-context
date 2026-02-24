# #614 Fix signed integer overflow in exponent parsing [Merged]

> Username: grisumbras  
> Created at: 2021-08-18 20:43:19 UTC  
> Updated at: 2021-10-20 19:41:10 UTC  
> Merged at: 2021-08-20 10:15:25 UTC  
> Closed at: 2021-08-20 10:15:25 UTC  
> Url: https://github.com/boostorg/json/pull/614  

Fix #612

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2021-08-18 21:17:51 UTC  
> Updated_at: 2021-08-20 09:13:22 UTC  
> Url: https://github.com/boostorg/json/pull/614#issuecomment-901436651  

# [Codecov](https://codecov.io/gh/boostorg/json/pull/614?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#614](https://codecov.io/gh/boostorg/json/pull/614?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (5b45854) into [develop](https://codecov.io/gh/boostorg/json/commit/3f48a274589aac06280b2c1b0e3d70796cac4b05?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (3f48a27) will **increase** coverage by `0.00%`.  
> The diff coverage is `100.00%`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/json/pull/614/graphs/tree.svg?width=650&height=150&src=pr&token=HNiMmIjyKi&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://codecov.io/gh/boostorg/json/pull/614?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@           Coverage Diff            @@  
##           develop     #614   +/-   ##  
========================================  
  Coverage    99.05%   99.05%             
========================================  
  Files           68       68             
  Lines         6114     6119    +5       
========================================  
+ Hits          6056     6061    +5       
  Misses          58       58             
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/json/pull/614?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/json/basic\_parser\_impl.hpp](https://codecov.io/gh/boostorg/json/pull/614/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9qc29uL2Jhc2ljX3BhcnNlcl9pbXBsLmhwcA==) | `99.19% <100.00%> (+<0.01%)` | :arrow_up: |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/json/pull/614?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/json/pull/614?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [3f48a27...5b45854](https://codecov.io/gh/boostorg/json/pull/614?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).

---

## Comment 2

> Username: pauldreik  
> Created_at: 2021-08-19 04:27:40 UTC  
> Url: https://github.com/boostorg/json/pull/614#issuecomment-901600244  

I can (in addition to the fuzz CI job with the test cases) confirm this fixes the problem the fuzzer found. I tested all the test cases from https://oss-fuzz.com/testcases?q=group%3A5898123298340864

---

## Comment 3

> Username: cppalliance-bot  
> Created_at: 2021-08-19 07:49:03 UTC  
> Url: https://github.com/boostorg/json/pull/614#issuecomment-901689375  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/614/pullrequest-condensed-b683eac.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/614/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/614/pullrequest.html)

---

## Comment 4

> Username: cppalliance-bot  
> Created_at: 2021-08-20 10:09:04 UTC  
> Url: https://github.com/boostorg/json/pull/614#issuecomment-902586713  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/614/pullrequest-condensed-7516f93.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/614/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/614/pullrequest.html)

---
