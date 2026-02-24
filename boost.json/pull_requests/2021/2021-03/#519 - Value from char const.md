# #519 Value from char const* [Merged]

> Username: grisumbras  
> Created at: 2021-03-04 19:13:08 UTC  
> Updated at: 2021-03-17 13:50:43 UTC  
> Merged at: 2021-03-04 21:48:58 UTC  
> Closed at: 2021-03-04 21:48:59 UTC  
> Url: https://github.com/boostorg/json/pull/519  

Fixes #507.

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2021-03-04 19:28:35 UTC  
> Updated_at: 2021-03-04 19:31:01 UTC  
> Url: https://github.com/boostorg/json/pull/519#issuecomment-790870964  

# [Codecov](https://codecov.io/gh/boostorg/json/pull/519?src=pr&el=h1) Report  
> Merging [#519](https://codecov.io/gh/boostorg/json/pull/519?src=pr&el=desc) (e580ace) into [develop](https://codecov.io/gh/boostorg/json/commit/030caa9d9a351d83c2c4878ee06b1d266227bc46?el=desc) (030caa9) will **not change** coverage.  
> The diff coverage is `n/a`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/json/pull/519/graphs/tree.svg?width=650&height=150&src=pr&token=HNiMmIjyKi)](https://codecov.io/gh/boostorg/json/pull/519?src=pr&el=tree)  
  
```diff  
@@           Coverage Diff            @@  
##           develop     #519   +/-   ##  
========================================  
  Coverage    99.10%   99.10%             
========================================  
  Files           67       67             
  Lines         6049     6049             
========================================  
  Hits          5995     5995             
  Misses          54       54             
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/json/pull/519?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [include/boost/json/detail/value\_traits.hpp](https://codecov.io/gh/boostorg/json/pull/519/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9qc29uL2RldGFpbC92YWx1ZV90cmFpdHMuaHBw) | `100.00% <ø> (ø)` | |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/json/pull/519?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/json/pull/519?src=pr&el=footer). Last update [030caa9...e580ace](https://codecov.io/gh/boostorg/json/pull/519?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---

## Comment 2

> Username: cppalliance-bot  
> Created_at: 2021-03-04 20:29:03 UTC  
> Url: https://github.com/boostorg/json/pull/519#issuecomment-790918739  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/519/pullrequest-condensed-7ed3ee6.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/519/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/519/pullrequest.html)

---

## Review 3 [Commented]

> Username: vinniefalco  
> Created_at: 2021-03-04 21:44:25 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/519#pullrequestreview-604590237  

📁 test/value_from.cpp

```diff
 185 |+ 
 186 |+         char const* s = "5";
 187 |+         testValueCtor(s);
```

> Username: vinniefalco  
> Created_at: 2021-03-04 21:44:25 UTC  
> Url: https://github.com/boostorg/json/pull/519#discussion_r587848191  

It looks like these tests are incomplete? `testValueCtor` is only called from 1 or 2 places. I'm not sure what is supposed to be happening here.


---
