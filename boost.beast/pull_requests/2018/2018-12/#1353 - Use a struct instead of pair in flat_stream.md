# #1353 Use a struct instead of pair in flat_stream [Closed]

> Username: djarek  
> Created at: 2018-12-06 22:27:02 UTC  
> Updated at: 2019-09-10 21:03:38 UTC  
> Closed at: 2018-12-07 13:09:26 UTC  
> Url: https://github.com/boostorg/beast/pull/1353  

No need to instantiate a pair and a custom struct has better member names.  
Doesn't affect public APIs.

---

## Comment 1

> Username: vinniefalco  
> Created_at: 2018-12-06 22:31:12 UTC  
> Url: https://github.com/boostorg/beast/pull/1353#issuecomment-445053917  

Seems reasonable

---

## Comment 2

> Username: codecov[bot]  
> Created_at: 2018-12-06 22:59:28 UTC  
> Url: https://github.com/boostorg/beast/pull/1353#issuecomment-445061326  

# [Codecov](https://codecov.io/gh/boostorg/beast/pull/1353?src=pr&el=h1) Report  
> Merging [#1353](https://codecov.io/gh/boostorg/beast/pull/1353?src=pr&el=desc) into [develop](https://codecov.io/gh/boostorg/beast/commit/4b714115519817a94089fb2371f759cd57b8da48?src=pr&el=desc) will **not change** coverage.  
> The diff coverage is `n/a`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/beast/pull/1353/graphs/tree.svg?width=650&token=Gq6MFA9JRF&height=150&src=pr)](https://codecov.io/gh/boostorg/beast/pull/1353?src=pr&el=tree)  
  
```diff  
@@           Coverage Diff            @@  
##           develop    #1353   +/-   ##  
========================================  
  Coverage    92.77%   92.77%             
========================================  
  Files          109      109             
  Lines        10886    10886             
========================================  
  Hits         10099    10099             
  Misses         787      787  
```  
  
  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/beast/pull/1353?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/beast/pull/1353?src=pr&el=footer). Last update [4b71411...faa37d6](https://codecov.io/gh/boostorg/beast/pull/1353?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---

## Comment 3

> Username: vinniefalco  
> Created_at: 2018-12-07 13:09:26 UTC  
> Url: https://github.com/boostorg/beast/pull/1353#issuecomment-445227945  

Merged in 197

---
