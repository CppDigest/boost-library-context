# #359 value::emplace takes arguments [Closed]

> Username: vinniefalco  
> Created at: 2020-09-18 00:21:39 UTC  
> Updated at: 2020-09-18 01:27:11 UTC  
> Closed at: 2020-09-18 00:49:26 UTC  
> Url: https://github.com/boostorg/json/pull/359  

This adds arguments to value::emplace

---

## Comment 1

> Username: vinniefalco  
> Created_at: 2020-09-18 00:49:26 UTC  
> Url: https://github.com/boostorg/json/pull/359#issuecomment-694581197  

Closing this because the algorithm is less efficient than using the noexcept emplace with no args followed by a `resize` (e.g. for arrays)

---

## Comment 2

> Username: cppalliance-bot  
> Created_at: 2020-09-18 01:27:11 UTC  
> Url: https://github.com/boostorg/json/pull/359#issuecomment-694594706  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/359/pullrequest-condensed-ee8e9c9.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/359/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/359/pullrequest.html)

---
