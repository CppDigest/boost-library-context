# #1587 Fix uninitalized memory use in deflate_stream [Closed]

> Username: djarek  
> Created at: 2019-04-23 00:26:15 UTC  
> Updated at: 2023-08-14 03:34:41 UTC  
> Closed at: 2019-05-04 16:31:26 UTC  
> Url: https://github.com/boostorg/beast/pull/1587  

Reference in original zlib:  
https://github.com/madler/zlib/pull/393

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2019-04-23 02:45:59 UTC  
> Url: https://github.com/boostorg/beast/pull/1587#issuecomment-485622101  

# [Codecov](https://codecov.io/gh/boostorg/beast/pull/1587?src=pr&el=h1) Report  
> Merging [#1587](https://codecov.io/gh/boostorg/beast/pull/1587?src=pr&el=desc) into [develop](https://codecov.io/gh/boostorg/beast/commit/41040a21f0b0e12537beb1262c2d538a023efe65?src=pr&el=desc) will **increase** coverage by `<.01%`.  
> The diff coverage is `100%`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/beast/pull/1587/graphs/tree.svg?width=650&token=Gq6MFA9JRF&height=150&src=pr)](https://codecov.io/gh/boostorg/beast/pull/1587?src=pr&el=tree)  
  
```diff  
@@            Coverage Diff             @@  
##           develop   #1587      +/-   ##  
==========================================  
+ Coverage     92.6%   92.6%   +<.01%       
==========================================  
  Files          151     151                
  Lines        12131   12132       +1       
==========================================  
+ Hits         11234   11235       +1       
  Misses         897     897  
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/beast/pull/1587?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [include/boost/beast/zlib/detail/deflate\_stream.ipp](https://codecov.io/gh/boostorg/beast/pull/1587/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC96bGliL2RldGFpbC9kZWZsYXRlX3N0cmVhbS5pcHA=) | `80.12% <100%> (+0.02%)` | :arrow_up: |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/beast/pull/1587?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/beast/pull/1587?src=pr&el=footer). Last update [41040a2...753a598](https://codecov.io/gh/boostorg/beast/pull/1587?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---

## Comment 2

> Username: Neustradamus  
> Created_at: 2023-08-14 03:34:41 UTC  
> Url: https://github.com/boostorg/beast/pull/1587#issuecomment-1676621002  

@djarek: Please look upstream.

---
