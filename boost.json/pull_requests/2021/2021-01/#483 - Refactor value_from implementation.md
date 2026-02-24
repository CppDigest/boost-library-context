# #483 Refactor value_from implementation [Closed]

> Username: grisumbras  
> Created at: 2021-01-12 14:39:39 UTC  
> Updated at: 2021-03-18 05:29:10 UTC  
> Closed at: 2021-01-12 23:10:43 UTC  
> Url: https://github.com/boostorg/json/pull/483  

Remove internal uses of tag_invoke, only using it from now on for user  
customizations. Fixes #481

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2021-01-12 15:04:17 UTC  
> Updated_at: 2021-01-12 15:06:36 UTC  
> Url: https://github.com/boostorg/json/pull/483#issuecomment-758715546  

# [Codecov](https://codecov.io/gh/boostorg/json/pull/483?src=pr&el=h1) Report  
> Merging [#483](https://codecov.io/gh/boostorg/json/pull/483?src=pr&el=desc) (16cd2bb) into [develop](https://codecov.io/gh/boostorg/json/commit/697ceeedbbc8e9800959949aa496926dff97bed5?el=desc) (697ceee) will **not change** coverage.  
> The diff coverage is `100.00%`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/json/pull/483/graphs/tree.svg?width=650&height=150&src=pr&token=HNiMmIjyKi)](https://codecov.io/gh/boostorg/json/pull/483?src=pr&el=tree)  
  
```diff  
@@           Coverage Diff            @@  
##           develop     #483   +/-   ##  
========================================  
  Coverage    99.10%   99.10%             
========================================  
  Files           67       67             
  Lines         6047     6047             
========================================  
  Hits          5993     5993             
  Misses          54       54             
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/json/pull/483?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [include/boost/json/detail/value\_from.hpp](https://codecov.io/gh/boostorg/json/pull/483/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9qc29uL2RldGFpbC92YWx1ZV9mcm9tLmhwcA==) | `100.00% <100.00%> (ø)` | |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/json/pull/483?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/json/pull/483?src=pr&el=footer). Last update [697ceee...16cd2bb](https://codecov.io/gh/boostorg/json/pull/483?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---

## Comment 2

> Username: cppalliance-bot  
> Created_at: 2021-01-12 15:53:42 UTC  
> Url: https://github.com/boostorg/json/pull/483#issuecomment-758751734  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/483/pullrequest-condensed-0cb322f.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/483/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/483/pullrequest.html)

---

## Comment 3

> Username: vinniefalco  
> Created_at: 2021-01-12 22:43:40 UTC  
> Url: https://github.com/boostorg/json/pull/483#issuecomment-759084528  

@pdimov is this what you expect the implementation to look like?

---

## Review 4 [Commented]

> Username: vinniefalco  
> Created_at: 2021-01-12 22:48:20 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/483#pullrequestreview-566765943  

📁 test/value_from.cpp

```diff
 263 |+     static
 264 |+     void
 265 |+     testPreferUserCustomizations()
```

> Username: vinniefalco  
> Created_at: 2021-01-12 22:48:20 UTC  
> Url: https://github.com/boostorg/json/pull/483#discussion_r556147875  

Maybe rename this function to `testIssue481` ?


---
