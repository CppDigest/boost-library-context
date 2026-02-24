# #1293 Compile ASIO into a static lib [Closed]

> Username: djarek  
> Created at: 2018-11-09 20:26:13 UTC  
> Updated at: 2018-11-23 18:14:58 UTC  
> Closed at: 2018-11-21 14:13:57 UTC  
> Url: https://github.com/boostorg/beast/pull/1293  



---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2018-11-10 00:17:30 UTC  
> Updated_at: 2018-11-13 17:22:23 UTC  
> Url: https://github.com/boostorg/beast/pull/1293#issuecomment-437534838  

# [Codecov](https://codecov.io/gh/boostorg/beast/pull/1293?src=pr&el=h1) Report  
> Merging [#1293](https://codecov.io/gh/boostorg/beast/pull/1293?src=pr&el=desc) into [develop](https://codecov.io/gh/boostorg/beast/commit/1ca690db0615a170c155683968071ee9eb609bc0?src=pr&el=desc) will **decrease** coverage by `<.01%`.  
> The diff coverage is `n/a`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/beast/pull/1293/graphs/tree.svg?width=650&token=Gq6MFA9JRF&height=150&src=pr)](https://codecov.io/gh/boostorg/beast/pull/1293?src=pr&el=tree)  
  
```diff  
@@             Coverage Diff             @@  
##           develop    #1293      +/-   ##  
===========================================  
- Coverage    95.36%   95.35%   -0.01%       
===========================================  
  Files          113      113                
  Lines        11117    11117                
===========================================  
- Hits         10602    10601       -1       
- Misses         515      516       +1  
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/beast/pull/1293?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [include/boost/beast/core/impl/buffers\_suffix.ipp](https://codecov.io/gh/boostorg/beast/pull/1293/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9jb3JlL2ltcGwvYnVmZmVyc19zdWZmaXguaXBw) | `98.8% <0%> (-1.2%)` | :arrow_down: |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/beast/pull/1293?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/beast/pull/1293?src=pr&el=footer). Last update [1ca690d...405dd9f](https://codecov.io/gh/boostorg/beast/pull/1293?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---

## Review 2 [Commented]

> Username: vinniefalco  
> Created_at: 2018-11-13 16:19:16 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/1293#pullrequestreview-174455791  

📁 tools/build-and-test.sh

```diff
  20 |+ if [[ "${BEAST_RETRY}" == "true" ]]; then
  21 |+   JOBS=1
  22 |+ elif [[ "${TRAVIS}" == "true" ]]; then
```

> Username: vinniefalco  
> Created_at: 2018-11-13 16:19:15 UTC  
> Updated_at: 2018-11-13 16:28:35 UTC  
> Url: https://github.com/boostorg/beast/pull/1293#discussion_r233115869  

So... on the first try you use 2 jobs, on subsequent tries 1 job? That's rather...brilliant :)

> Username: djarek  
> Created_at: 2018-11-13 16:25:27 UTC  
> Updated_at: 2018-11-13 16:28:35 UTC  
> Url: https://github.com/boostorg/beast/pull/1293#discussion_r233118558  

Yeah, couldn't figure a better way.


---
