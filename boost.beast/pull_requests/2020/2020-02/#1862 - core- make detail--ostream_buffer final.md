# #1862 core: make detail::ostream_buffer final [Closed]

> Username: reddwarf69  
> Created at: 2020-02-28 13:08:47 UTC  
> Updated at: 2020-02-29 22:32:20 UTC  
> Closed at: 2020-02-29 22:32:20 UTC  
> Url: https://github.com/boostorg/beast/pull/1862  

The Clang Static Analyzer was complaining about the fact the destructor  
calls a virtual function. This silences it.

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2020-02-28 13:22:34 UTC  
> Updated_at: 2020-02-28 19:22:46 UTC  
> Url: https://github.com/boostorg/beast/pull/1862#issuecomment-592510102  

# [Codecov](https://codecov.io/gh/boostorg/beast/pull/1862?src=pr&el=h1) Report  
> Merging [#1862](https://codecov.io/gh/boostorg/beast/pull/1862?src=pr&el=desc) into [develop](https://codecov.io/gh/boostorg/beast/commit/c058567ec1b6b767343706cdc58f45e69d491dda&el=desc) will **decrease** coverage by `0.00%`.  
> The diff coverage is `n/a`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/beast/pull/1862/graphs/tree.svg?width=650&height=150&src=pr&token=Gq6MFA9JRF)](https://codecov.io/gh/boostorg/beast/pull/1862?src=pr&el=tree)  
  
```diff  
@@             Coverage Diff             @@  
##           develop    #1862      +/-   ##  
===========================================  
- Coverage    95.17%   95.16%   -0.01%       
===========================================  
  Files          156      156                
  Lines        11942    11942                
===========================================  
- Hits         11366    11365       -1       
- Misses         576      577       +1       
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/beast/pull/1862?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [include/boost/beast/zlib/detail/inflate\_stream.ipp](https://codecov.io/gh/boostorg/beast/pull/1862/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC96bGliL2RldGFpbC9pbmZsYXRlX3N0cmVhbS5pcHA=) | `94.37% <0.00%> (-0.20%)` | :arrow_down: |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/beast/pull/1862?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/beast/pull/1862?src=pr&el=footer). Last update [c058567...ff8af50](https://codecov.io/gh/boostorg/beast/pull/1862?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---

## Comment 2

> Username: vinniefalco  
> Created_at: 2020-02-28 17:17:41 UTC  
> Url: https://github.com/boostorg/beast/pull/1862#issuecomment-592613642  

Commit message should read "detail::ostream_buffer is final"

---
