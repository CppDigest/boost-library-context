# #497 Fix (lack of) building without C++11 support [Closed]

> Username: grisumbras  
> Created at: 2021-02-05 18:23:48 UTC  
> Updated at: 2021-03-10 11:30:26 UTC  
> Closed at: 2021-02-09 18:14:45 UTC  
> Url: https://github.com/boostorg/json/pull/497  



---

## Comment 1

> Username: grisumbras  
> Created_at: 2021-02-05 18:24:34 UTC  
> Url: https://github.com/boostorg/json/pull/497#issuecomment-774205793  

This should fix Boost builds without C++11 support

---

## Comment 2

> Username: vinniefalco  
> Created_at: 2021-02-05 18:27:02 UTC  
> Url: https://github.com/boostorg/json/pull/497#issuecomment-774207087  

Suggested commit message: "Builds prior to C++11 are no-ops"

---

## Comment 3

> Username: codecov[bot]  
> Created_at: 2021-02-05 18:42:13 UTC  
> Updated_at: 2021-02-09 17:56:42 UTC  
> Url: https://github.com/boostorg/json/pull/497#issuecomment-774216774  

# [Codecov](https://codecov.io/gh/boostorg/json/pull/497?src=pr&el=h1) Report  
> Merging [#497](https://codecov.io/gh/boostorg/json/pull/497?src=pr&el=desc) (2f1c258) into [develop](https://codecov.io/gh/boostorg/json/commit/f0ea69fefda1c8b02075814e32d86a74e216cc59?el=desc) (f0ea69f) will **not change** coverage.  
> The diff coverage is `n/a`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/json/pull/497/graphs/tree.svg?width=650&height=150&src=pr&token=HNiMmIjyKi)](https://codecov.io/gh/boostorg/json/pull/497?src=pr&el=tree)  
  
```diff  
@@           Coverage Diff            @@  
##           develop     #497   +/-   ##  
========================================  
  Coverage    99.10%   99.10%             
========================================  
  Files           67       67             
  Lines         6047     6047             
========================================  
  Hits          5993     5993             
  Misses          54       54             
```  
  
  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/json/pull/497?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/json/pull/497?src=pr&el=footer). Last update [f0ea69f...ffc82d3](https://codecov.io/gh/boostorg/json/pull/497?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---

## Comment 4

> Username: grisumbras  
> Created_at: 2021-02-05 19:56:35 UTC  
> Url: https://github.com/boostorg/json/pull/497#issuecomment-774254691  

Do you think there's a need for CI test for no-op build with cxxstd=03?

---

## Comment 5

> Username: vinniefalco  
> Created_at: 2021-02-05 21:00:05 UTC  
> Url: https://github.com/boostorg/json/pull/497#issuecomment-774285838  

Please change "Adds back GCC 4.8 support" to "gcc-4.8 is supported"

---

## Comment 6

> Username: vinniefalco  
> Created_at: 2021-02-05 21:00:15 UTC  
> Url: https://github.com/boostorg/json/pull/497#issuecomment-774285924  

> Do you think there's a need for CI test for no-op build with cxxstd=03?  
  
Up to you

---

## Comment 7

> Username: cppalliance-bot  
> Created_at: 2021-02-05 21:08:42 UTC  
> Url: https://github.com/boostorg/json/pull/497#issuecomment-774289660  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/497/pullrequest-condensed-17ba2d3.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/497/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/497/pullrequest.html)

---

## Comment 8

> Username: cppalliance-bot  
> Created_at: 2021-02-08 16:48:42 UTC  
> Url: https://github.com/boostorg/json/pull/497#issuecomment-775283279  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/497/pullrequest-condensed-68b97bd.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/497/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/497/pullrequest.html)

---

## Comment 9

> Username: vinniefalco  
> Created_at: 2021-02-08 17:56:35 UTC  
> Url: https://github.com/boostorg/json/pull/497#issuecomment-775330003  

Can we please change the commit message to "gcc-4.8 is supported" ? Use an interactive rebase (`git rebase -i`) then force-push the branch.

---

## Comment 10

> Username: grisumbras  
> Created_at: 2021-02-08 17:58:10 UTC  
> Url: https://github.com/boostorg/json/pull/497#issuecomment-775330984  

Which commit?

---

## Comment 11

> Username: vinniefalco  
> Created_at: 2021-02-08 18:28:15 UTC  
> Url: https://github.com/boostorg/json/pull/497#issuecomment-775350464  

This one:   
![image](https://user-images.githubusercontent.com/1503976/107264844-58f21c80-69f8-11eb-9567-623c2ea0b4ba.png)

---

## Comment 12

> Username: cppalliance-bot  
> Created_at: 2021-02-09 19:08:42 UTC  
> Url: https://github.com/boostorg/json/pull/497#issuecomment-776171808  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/497/pullrequest-condensed-2d6a12e.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/497/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/497/pullrequest.html)

---
