# #1570 Use `std::launder` for pointers to placement-new constructed objects [Closed]

> Username: djarek  
> Created at: 2019-04-13 00:16:55 UTC  
> Updated at: 2019-09-10 21:04:09 UTC  
> Closed at: 2019-04-15 16:04:05 UTC  
> Url: https://github.com/boostorg/beast/pull/1570  

`std::launder` must be used whenever placement-new of an unknown type is performed and the result of the placement-new expression is discarded.

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2019-04-13 03:04:48 UTC  
> Url: https://github.com/boostorg/beast/pull/1570#issuecomment-482771009  

# [Codecov](https://codecov.io/gh/boostorg/beast/pull/1570?src=pr&el=h1) Report  
> Merging [#1570](https://codecov.io/gh/boostorg/beast/pull/1570?src=pr&el=desc) into [develop](https://codecov.io/gh/boostorg/beast/commit/0345aa194ddfd2b24fd0d0539798dd672e076772?src=pr&el=desc) will **increase** coverage by `<.01%`.  
> The diff coverage is `100%`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/beast/pull/1570/graphs/tree.svg?width=650&token=Gq6MFA9JRF&height=150&src=pr)](https://codecov.io/gh/boostorg/beast/pull/1570?src=pr&el=tree)  
  
```diff  
@@             Coverage Diff             @@  
##           develop    #1570      +/-   ##  
===========================================  
+ Coverage    92.61%   92.61%   +<.01%       
===========================================  
  Files          150      151       +1       
  Lines        12102    12106       +4       
===========================================  
+ Hits         11208    11212       +4       
  Misses         894      894  
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/beast/pull/1570?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [include/boost/beast/websocket/detail/decorator.hpp](https://codecov.io/gh/boostorg/beast/pull/1570/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC93ZWJzb2NrZXQvZGV0YWlsL2RlY29yYXRvci5ocHA=) | `91.35% <100%> (+0.21%)` | :arrow_up: |  
| [include/boost/beast/core/detail/type\_traits.hpp](https://codecov.io/gh/boostorg/beast/pull/1570/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9jb3JlL2RldGFpbC90eXBlX3RyYWl0cy5ocHA=) | `100% <100%> (ø)` | |  
| [include/boost/beast/core/detail/variant.hpp](https://codecov.io/gh/boostorg/beast/pull/1570/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9jb3JlL2RldGFpbC92YXJpYW50LmhwcA==) | `100% <100%> (ø)` | :arrow_up: |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/beast/pull/1570?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/beast/pull/1570?src=pr&el=footer). Last update [0345aa1...47b4549](https://codecov.io/gh/boostorg/beast/pull/1570?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---
