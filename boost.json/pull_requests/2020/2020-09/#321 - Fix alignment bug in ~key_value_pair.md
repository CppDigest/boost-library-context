# #321 Fix alignment bug in ~key_value_pair [Closed]

> Username: sdkrystian  
> Created at: 2020-09-12 02:41:55 UTC  
> Updated at: 2020-09-14 02:09:17 UTC  
> Closed at: 2020-09-14 02:09:17 UTC  
> Url: https://github.com/boostorg/json/pull/321  



---

## Comment 1

> Username: cppalliance-bot  
> Created_at: 2020-09-12 05:42:13 UTC  
> Url: https://github.com/boostorg/json/pull/321#issuecomment-691417486  

![pullrequest](http://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/321/pullrequest-condensed-8f5a08f.png)  
Benchmark test results. More info at [http://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/321/pullrequest.html](http://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/321/pullrequest.html)

---

## Comment 2

> Username: codecov[bot]  
> Created_at: 2020-09-12 14:24:48 UTC  
> Updated_at: 2020-09-12 16:06:12 UTC  
> Url: https://github.com/boostorg/json/pull/321#issuecomment-691497332  

# [Codecov](https://codecov.io/gh/CPPAlliance/json/pull/321?src=pr&el=h1) Report  
> Merging [#321](https://codecov.io/gh/CPPAlliance/json/pull/321?src=pr&el=desc) into [develop](https://codecov.io/gh/CPPAlliance/json/commit/7b7a977fd469fe3caf6c2bf17e5e190eb587d741?el=desc) will **not change** coverage.  
> The diff coverage is `100.00%`.  
  
[![Impacted file tree graph](https://codecov.io/gh/CPPAlliance/json/pull/321/graphs/tree.svg?width=650&height=150&src=pr&token=HNiMmIjyKi)](https://codecov.io/gh/CPPAlliance/json/pull/321?src=pr&el=tree)  
  
```diff  
@@           Coverage Diff            @@  
##           develop     #321   +/-   ##  
========================================  
  Coverage    97.37%   97.37%             
========================================  
  Files           69       69             
  Lines         5757     5757             
========================================  
  Hits          5606     5606             
  Misses         151      151             
```  
  
  
| [Impacted Files](https://codecov.io/gh/CPPAlliance/json/pull/321?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [include/boost/json/impl/value.ipp](https://codecov.io/gh/CPPAlliance/json/pull/321/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9qc29uL2ltcGwvdmFsdWUuaXBw) | `97.11% <100.00%> (ø)` | |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/CPPAlliance/json/pull/321?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/CPPAlliance/json/pull/321?src=pr&el=footer). Last update [7b7a977...94a3d6e](https://codecov.io/gh/CPPAlliance/json/pull/321?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---

## Review 3 [Commented]

> Username: vinniefalco  
> Created_at: 2020-09-14 00:46:19 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/321#pullrequestreview-487358415  

📁 include/boost/json/impl/value.ipp

```diff
 457 |     sp->deallocate(const_cast<char*>(key_),
 458 |-         len_ + 1, 1);
 458 |+         len_ + 1);
```

> Username: vinniefalco  
> Created_at: 2020-09-14 00:46:19 UTC  
> Updated_at: 2020-09-14 00:46:20 UTC  
> Url: https://github.com/boostorg/json/pull/321#discussion_r487601051  

Wouldn't it be better to change the alignment in the call to `allocate` to be 1?


---
