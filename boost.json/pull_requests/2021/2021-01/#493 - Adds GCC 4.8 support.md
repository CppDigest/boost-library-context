# #493 Adds GCC 4.8 support [Closed]

> Username: grisumbras  
> Created at: 2021-01-28 08:59:19 UTC  
> Updated at: 2021-03-18 05:29:06 UTC  
> Closed at: 2021-03-01 13:52:13 UTC  
> Url: https://github.com/boostorg/json/pull/493  

Fixes #491   
GCC 4.8 has alignas support, but has bugs. These bugs do not, however,  
affect Boost.Json, as it requires only the basic functionality. This  
commit replaces Boost.Config's check for alignas with a custom one, that  
tests for alignas functionality that is used in this library.

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2021-01-28 09:23:13 UTC  
> Updated_at: 2021-01-28 09:25:26 UTC  
> Url: https://github.com/boostorg/json/pull/493#issuecomment-768918377  

# [Codecov](https://codecov.io/gh/boostorg/json/pull/493?src=pr&el=h1) Report  
> Merging [#493](https://codecov.io/gh/boostorg/json/pull/493?src=pr&el=desc) (f0229a2) into [develop](https://codecov.io/gh/boostorg/json/commit/932b97e5ce899f3faebb7b7ab5a68b023131b77f?el=desc) (932b97e) will **not change** coverage.  
> The diff coverage is `n/a`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/json/pull/493/graphs/tree.svg?width=650&height=150&src=pr&token=HNiMmIjyKi)](https://codecov.io/gh/boostorg/json/pull/493?src=pr&el=tree)  
  
```diff  
@@           Coverage Diff            @@  
##           develop     #493   +/-   ##  
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
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/json/pull/493?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/json/pull/493?src=pr&el=footer). Last update [932b97e...f0229a2](https://codecov.io/gh/boostorg/json/pull/493?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---

## Comment 2

> Username: cppalliance-bot  
> Created_at: 2021-01-28 10:13:42 UTC  
> Url: https://github.com/boostorg/json/pull/493#issuecomment-768947572  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/493/pullrequest-condensed-a43ac89.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/493/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/493/pullrequest.html)

---
