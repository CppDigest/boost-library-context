# #1866 After calling the DynamicBuffer commit function the buffer received via [Closed]

> Username: madmongo1  
> Created at: 2020-02-29 07:26:34 UTC  
> Updated at: 2020-02-29 22:32:22 UTC  
> Closed at: 2020-02-29 22:32:22 UTC  
> Url: https://github.com/boostorg/beast/pull/1866  

fix ostream flush:  
  
prepare() can be invalidated but the ostream kept using it.  
  
fix #1853  
close #1859

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2020-02-29 07:39:06 UTC  
> Url: https://github.com/boostorg/beast/pull/1866#issuecomment-592915401  

# [Codecov](https://codecov.io/gh/boostorg/beast/pull/1866?src=pr&el=h1) Report  
> Merging [#1866](https://codecov.io/gh/boostorg/beast/pull/1866?src=pr&el=desc) into [develop](https://codecov.io/gh/boostorg/beast/commit/c058567ec1b6b767343706cdc58f45e69d491dda?src=pr&el=desc) will **increase** coverage by `<.01%`.  
> The diff coverage is `100%`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/beast/pull/1866/graphs/tree.svg?width=650&token=Gq6MFA9JRF&height=150&src=pr)](https://codecov.io/gh/boostorg/beast/pull/1866?src=pr&el=tree)  
  
```diff  
@@             Coverage Diff             @@  
##           develop    #1866      +/-   ##  
===========================================  
+ Coverage    95.17%   95.17%   +<.01%       
===========================================  
  Files          156      156                
  Lines        11942    11943       +1       
===========================================  
+ Hits         11366    11367       +1       
  Misses         576      576  
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/beast/pull/1866?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [include/boost/beast/core/detail/ostream.hpp](https://codecov.io/gh/boostorg/beast/pull/1866/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9jb3JlL2RldGFpbC9vc3RyZWFtLmhwcA==) | `100% <100%> (ø)` | :arrow_up: |  
| [include/boost/beast/zlib/detail/inflate\_stream.ipp](https://codecov.io/gh/boostorg/beast/pull/1866/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC96bGliL2RldGFpbC9pbmZsYXRlX3N0cmVhbS5pcHA=) | `94.37% <0%> (-0.2%)` | :arrow_down: |  
| [include/boost/beast/zlib/detail/deflate\_stream.ipp](https://codecov.io/gh/boostorg/beast/pull/1866/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC96bGliL2RldGFpbC9kZWZsYXRlX3N0cmVhbS5pcHA=) | `88.6% <0%> (+0.1%)` | :arrow_up: |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/beast/pull/1866?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/beast/pull/1866?src=pr&el=footer). Last update [c058567...7806290](https://codecov.io/gh/boostorg/beast/pull/1866?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---
