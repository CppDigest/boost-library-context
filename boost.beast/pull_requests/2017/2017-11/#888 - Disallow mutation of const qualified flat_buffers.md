# #888 Disallow mutation of const qualified flat_buffers [Closed]

> Username: djarek  
> Created at: 2017-11-13 23:04:01 UTC  
> Updated at: 2019-09-10 21:02:11 UTC  
> Closed at: 2017-11-14 17:22:14 UTC  
> Url: https://github.com/boostorg/beast/pull/888  

Returning a mutable buffer sequence from a const qualified DynamicBuffer may result in unexpected results when a callee mutates it.  
  
Resolves: #886

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2017-11-13 23:17:53 UTC  
> Url: https://github.com/boostorg/beast/pull/888#issuecomment-344092064  

# [Codecov](https://codecov.io/gh/boostorg/beast/pull/888?src=pr&el=h1) Report  
> Merging [#888](https://codecov.io/gh/boostorg/beast/pull/888?src=pr&el=desc) into [develop](https://codecov.io/gh/boostorg/beast/commit/fe39a7c22a9423ad7a217d62fe54bc0e8940bce5?src=pr&el=desc) will **decrease** coverage by `<.01%`.  
> The diff coverage is `100%`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/beast/pull/888/graphs/tree.svg?src=pr&token=Gq6MFA9JRF&width=650&height=150)](https://codecov.io/gh/boostorg/beast/pull/888?src=pr&el=tree)  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #888      +/-   ##  
===========================================  
- Coverage    95.66%   95.65%   -0.01%       
===========================================  
  Files          104      104                
  Lines        10453    10455       +2       
===========================================  
+ Hits         10000    10001       +1       
- Misses         453      454       +1  
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/beast/pull/888?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [include/boost/beast/core/flat\_buffer.hpp](https://codecov.io/gh/boostorg/beast/pull/888?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9jb3JlL2ZsYXRfYnVmZmVyLmhwcA==) | `100% <100%> (ø)` | :arrow_up: |  
| [include/boost/beast/websocket/impl/close.ipp](https://codecov.io/gh/boostorg/beast/pull/888?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC93ZWJzb2NrZXQvaW1wbC9jbG9zZS5pcHA=) | `99.02% <0%> (-0.49%)` | :arrow_down: |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/beast/pull/888?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/beast/pull/888?src=pr&el=footer). Last update [fe39a7c...6f8a53e](https://codecov.io/gh/boostorg/beast/pull/888?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---

## Comment 2

> Username: djarek  
> Created_at: 2017-11-14 17:22:14 UTC  
> Url: https://github.com/boostorg/beast/pull/888#issuecomment-344332480  

Unfortunately, there's a larger design issue here, which requires a different solution.

---
