# #1864 Field digest is endian-independent: [Merged]

> Username: vinniefalco  
> Created at: 2020-02-28 16:49:35 UTC  
> Updated at: 2020-02-29 22:32:21 UTC  
> Merged at: 2020-02-29 22:32:21 UTC  
> Closed at: 2020-02-29 22:32:21 UTC  
> Url: https://github.com/boostorg/beast/pull/1864  

fix #1863  
  
* Added some comments  
* Add 64-bit specific calculation  
* Fix endian-dependence in digest

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2020-02-28 17:21:51 UTC  
> Updated_at: 2020-02-29 22:32:02 UTC  
> Url: https://github.com/boostorg/beast/pull/1864#issuecomment-592617231  

# [Codecov](https://codecov.io/gh/boostorg/beast/pull/1864?src=pr&el=h1) Report  
> Merging [#1864](https://codecov.io/gh/boostorg/beast/pull/1864?src=pr&el=desc) into [develop](https://codecov.io/gh/boostorg/beast/commit/c058567ec1b6b767343706cdc58f45e69d491dda?src=pr&el=desc) will **decrease** coverage by `0.07%`.  
> The diff coverage is `94.2%`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/beast/pull/1864/graphs/tree.svg?width=650&token=Gq6MFA9JRF&height=150&src=pr)](https://codecov.io/gh/boostorg/beast/pull/1864?src=pr&el=tree)  
  
```diff  
@@            Coverage Diff             @@  
##           develop   #1864      +/-   ##  
==========================================  
- Coverage    95.17%   95.1%   -0.08%       
==========================================  
  Files          156     156                
  Lines        11942   12011      +69       
==========================================  
+ Hits         11366   11423      +57       
- Misses         576     588      +12  
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/beast/pull/1864?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [include/boost/beast/core/impl/static\_buffer.ipp](https://codecov.io/gh/boostorg/beast/pull/1864/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9jb3JlL2ltcGwvc3RhdGljX2J1ZmZlci5pcHA=) | `100% <ø> (ø)` | :arrow_up: |  
| [include/boost/beast/core/flat\_static\_buffer.hpp](https://codecov.io/gh/boostorg/beast/pull/1864/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9jb3JlL2ZsYXRfc3RhdGljX2J1ZmZlci5ocHA=) | `100% <ø> (ø)` | :arrow_up: |  
| [include/boost/beast/core/buffers\_adaptor.hpp](https://codecov.io/gh/boostorg/beast/pull/1864/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9jb3JlL2J1ZmZlcnNfYWRhcHRvci5ocHA=) | `100% <ø> (ø)` | :arrow_up: |  
| [include/boost/beast/core/multi\_buffer.hpp](https://codecov.io/gh/boostorg/beast/pull/1864/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9jb3JlL211bHRpX2J1ZmZlci5ocHA=) | `100% <ø> (ø)` | :arrow_up: |  
| [include/boost/beast/core/flat\_buffer.hpp](https://codecov.io/gh/boostorg/beast/pull/1864/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9jb3JlL2ZsYXRfYnVmZmVyLmhwcA==) | `100% <ø> (ø)` | :arrow_up: |  
| [include/boost/beast/core/detail/buffer.hpp](https://codecov.io/gh/boostorg/beast/pull/1864/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9jb3JlL2RldGFpbC9idWZmZXIuaHBw) | `100% <ø> (ø)` | :arrow_up: |  
| [include/boost/beast/core/static\_buffer.hpp](https://codecov.io/gh/boostorg/beast/pull/1864/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9jb3JlL3N0YXRpY19idWZmZXIuaHBw) | `100% <ø> (ø)` | :arrow_up: |  
| [include/boost/beast/core/detail/ostream.hpp](https://codecov.io/gh/boostorg/beast/pull/1864/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9jb3JlL2RldGFpbC9vc3RyZWFtLmhwcA==) | `100% <100%> (ø)` | :arrow_up: |  
| [include/boost/beast/http/impl/field.ipp](https://codecov.io/gh/boostorg/beast/pull/1864/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9odHRwL2ltcGwvZmllbGQuaXBw) | `97.59% <100%> (+0.18%)` | :arrow_up: |  
| [include/boost/beast/core/impl/multi\_buffer.hpp](https://codecov.io/gh/boostorg/beast/pull/1864/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9jb3JlL2ltcGwvbXVsdGlfYnVmZmVyLmhwcA==) | `98.13% <90.72%> (-1.87%)` | :arrow_down: |  
| ... and [6 more](https://codecov.io/gh/boostorg/beast/pull/1864/diff?src=pr&el=tree-more) | |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/beast/pull/1864?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/beast/pull/1864?src=pr&el=footer). Last update [c058567...c8a726f](https://codecov.io/gh/boostorg/beast/pull/1864?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---
