# #1311 Remove redundant static_assert [Closed]

> Username: djarek  
> Created at: 2018-11-21 00:37:02 UTC  
> Updated at: 2019-09-10 21:02:22 UTC  
> Closed at: 2018-11-21 14:13:58 UTC  
> Url: https://github.com/boostorg/beast/pull/1311  

The standard library doesn't handle types that throw from destructors, so the check is redundant.

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2018-11-21 01:47:13 UTC  
> Url: https://github.com/boostorg/beast/pull/1311#issuecomment-440497977  

# [Codecov](https://codecov.io/gh/boostorg/beast/pull/1311?src=pr&el=h1) Report  
> Merging [#1311](https://codecov.io/gh/boostorg/beast/pull/1311?src=pr&el=desc) into [develop](https://codecov.io/gh/boostorg/beast/commit/1ca690db0615a170c155683968071ee9eb609bc0?src=pr&el=desc) will **decrease** coverage by `<.01%`.  
> The diff coverage is `n/a`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/beast/pull/1311/graphs/tree.svg?width=650&token=Gq6MFA9JRF&height=150&src=pr)](https://codecov.io/gh/boostorg/beast/pull/1311?src=pr&el=tree)  
  
```diff  
@@             Coverage Diff             @@  
##           develop    #1311      +/-   ##  
===========================================  
- Coverage    95.36%   95.35%   -0.01%       
===========================================  
  Files          113      113                
  Lines        11117    11117                
===========================================  
- Hits         10602    10601       -1       
- Misses         515      516       +1  
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/beast/pull/1311?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [include/boost/beast/core/handler\_ptr.hpp](https://codecov.io/gh/boostorg/beast/pull/1311/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9jb3JlL2hhbmRsZXJfcHRyLmhwcA==) | `100% <ø> (ø)` | :arrow_up: |  
| [include/boost/beast/core/impl/buffers\_suffix.ipp](https://codecov.io/gh/boostorg/beast/pull/1311/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9jb3JlL2ltcGwvYnVmZmVyc19zdWZmaXguaXBw) | `98.8% <0%> (-1.2%)` | :arrow_down: |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/beast/pull/1311?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/beast/pull/1311?src=pr&el=footer). Last update [1ca690d...bef0c13](https://codecov.io/gh/boostorg/beast/pull/1311?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---
