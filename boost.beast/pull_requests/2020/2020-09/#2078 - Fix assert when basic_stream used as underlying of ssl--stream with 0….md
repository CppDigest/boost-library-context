# #2078 Fix assert when basic_stream used as underlying of ssl::stream with 0… [Closed]

> Username: madmongo1  
> Created at: 2020-09-03 09:49:38 UTC  
> Updated at: 2020-09-14 06:58:31 UTC  
> Closed at: 2020-09-14 06:58:31 UTC  
> Url: https://github.com/boostorg/beast/pull/2078  

…-length write  
  
Fixes #2065

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2020-09-04 17:54:40 UTC  
> Url: https://github.com/boostorg/beast/pull/2078#issuecomment-687295777  

# [Codecov](https://codecov.io/gh/boostorg/beast/pull/2078?src=pr&el=h1) Report  
> Merging [#2078](https://codecov.io/gh/boostorg/beast/pull/2078?src=pr&el=desc) into [develop](https://codecov.io/gh/boostorg/beast/commit/4eb7d06d5d5b2b73c681f1a161cc50be697e8197?el=desc) will **decrease** coverage by `0.00%`.  
> The diff coverage is `100.00%`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/beast/pull/2078/graphs/tree.svg?width=650&height=150&src=pr&token=Gq6MFA9JRF)](https://codecov.io/gh/boostorg/beast/pull/2078?src=pr&el=tree)  
  
```diff  
@@             Coverage Diff             @@  
##           develop    #2078      +/-   ##  
===========================================  
- Coverage    95.06%   95.06%   -0.01%       
===========================================  
  Files          153      153                
  Lines        11945    11959      +14       
===========================================  
+ Hits         11356    11369      +13       
- Misses         589      590       +1       
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/beast/pull/2078?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [include/boost/beast/core/detail/stream\_base.hpp](https://codecov.io/gh/boostorg/beast/pull/2078/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9jb3JlL2RldGFpbC9zdHJlYW1fYmFzZS5ocHA=) | `100.00% <100.00%> (ø)` | |  
| [include/boost/beast/core/impl/basic\_stream.hpp](https://codecov.io/gh/boostorg/beast/pull/2078/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9jb3JlL2ltcGwvYmFzaWNfc3RyZWFtLmhwcA==) | `85.98% <100.00%> (+0.10%)` | :arrow_up: |  
| [include/boost/beast/zlib/detail/inflate\_stream.ipp](https://codecov.io/gh/boostorg/beast/pull/2078/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC96bGliL2RldGFpbC9pbmZsYXRlX3N0cmVhbS5pcHA=) | `94.37% <0.00%> (-0.20%)` | :arrow_down: |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/beast/pull/2078?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/beast/pull/2078?src=pr&el=footer). Last update [4eb7d06...5968bfe](https://codecov.io/gh/boostorg/beast/pull/2078?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---
