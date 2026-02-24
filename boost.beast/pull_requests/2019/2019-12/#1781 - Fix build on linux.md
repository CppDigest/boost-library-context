# #1781 Fix build on linux [Closed]

> Username: madmongo1  
> Created at: 2019-12-14 16:18:00 UTC  
> Updated at: 2020-01-22 15:36:51 UTC  
> Closed at: 2020-01-22 15:36:50 UTC  
> Url: https://github.com/boostorg/beast/pull/1781  

Minimal changes necessary to get the cmake project to build in a linux environment.

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2019-12-14 20:45:07 UTC  
> Updated_at: 2019-12-30 01:48:31 UTC  
> Url: https://github.com/boostorg/beast/pull/1781#issuecomment-565751639  

# [Codecov](https://codecov.io/gh/boostorg/beast/pull/1781?src=pr&el=h1) Report  
> Merging [#1781](https://codecov.io/gh/boostorg/beast/pull/1781?src=pr&el=desc) into [develop](https://codecov.io/gh/boostorg/beast/commit/0fa85226f0316eea13bb13d31aaec699cbc18eae?src=pr&el=desc) will **decrease** coverage by `<.01%`.  
> The diff coverage is `n/a`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/beast/pull/1781/graphs/tree.svg?width=650&token=Gq6MFA9JRF&height=150&src=pr)](https://codecov.io/gh/boostorg/beast/pull/1781?src=pr&el=tree)  
  
```diff  
@@             Coverage Diff             @@  
##           develop    #1781      +/-   ##  
===========================================  
- Coverage    95.17%   95.16%   -0.01%       
===========================================  
  Files          156      156                
  Lines        11949    11949                
===========================================  
- Hits         11372    11371       -1       
- Misses         577      578       +1  
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/beast/pull/1781?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [include/boost/beast/zlib/detail/deflate\_stream.ipp](https://codecov.io/gh/boostorg/beast/pull/1781/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC96bGliL2RldGFpbC9kZWZsYXRlX3N0cmVhbS5pcHA=) | `88.5% <0%> (-0.11%)` | :arrow_down: |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/beast/pull/1781?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/beast/pull/1781?src=pr&el=footer). Last update [0fa8522...c228bd2](https://codecov.io/gh/boostorg/beast/pull/1781?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---

## Comment 2

> Username: vinniefalco  
> Created_at: 2020-01-22 15:36:50 UTC  
> Url: https://github.com/boostorg/beast/pull/1781#issuecomment-577244558  

The cmake changes were merged in https://github.com/boostorg/beast/commit/7bf58e28e4e6119e6345d780c0da6c568717da26

---
