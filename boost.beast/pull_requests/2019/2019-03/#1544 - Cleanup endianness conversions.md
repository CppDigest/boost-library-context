# #1544 Cleanup endianness conversions: [Closed]

> Username: djarek  
> Created at: 2019-03-25 23:23:19 UTC  
> Updated at: 2019-09-10 21:04:00 UTC  
> Closed at: 2019-04-11 20:58:35 UTC  
> Url: https://github.com/boostorg/beast/pull/1544  

- Use Boost.Endian conversions functions.  
- Remove existing private conversion functions.

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2019-03-26 06:01:25 UTC  
> Url: https://github.com/boostorg/beast/pull/1544#issuecomment-476487160  

# [Codecov](https://codecov.io/gh/boostorg/beast/pull/1544?src=pr&el=h1) Report  
> Merging [#1544](https://codecov.io/gh/boostorg/beast/pull/1544?src=pr&el=desc) into [develop](https://codecov.io/gh/boostorg/beast/commit/2c141cd4ad55da087061e05206d9ff849aca0f63?src=pr&el=desc) will **decrease** coverage by `<.01%`.  
> The diff coverage is `100%`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/beast/pull/1544/graphs/tree.svg?width=650&token=Gq6MFA9JRF&height=150&src=pr)](https://codecov.io/gh/boostorg/beast/pull/1544?src=pr&el=tree)  
  
```diff  
@@             Coverage Diff             @@  
##           develop    #1544      +/-   ##  
===========================================  
- Coverage    92.66%   92.65%   -0.01%       
===========================================  
  Files          150      150                
  Lines        12120    12091      -29       
===========================================  
- Hits         11231    11203      -28       
+ Misses         889      888       -1  
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/beast/pull/1544?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [include/boost/beast/websocket/impl/stream.hpp](https://codecov.io/gh/boostorg/beast/pull/1544/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC93ZWJzb2NrZXQvaW1wbC9zdHJlYW0uaHBw) | `79.41% <ø> (ø)` | :arrow_up: |  
| [include/boost/beast/websocket/impl/stream\_impl.hpp](https://codecov.io/gh/boostorg/beast/pull/1544/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC93ZWJzb2NrZXQvaW1wbC9zdHJlYW1faW1wbC5ocHA=) | `96.58% <100%> (-0.02%)` | :arrow_down: |  
| [include/boost/beast/websocket/detail/frame.hpp](https://codecov.io/gh/boostorg/beast/pull/1544/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC93ZWJzb2NrZXQvZGV0YWlsL2ZyYW1lLmhwcA==) | `98.64% <100%> (-0.37%)` | :arrow_down: |  
| [include/boost/beast/zlib/detail/deflate\_stream.ipp](https://codecov.io/gh/boostorg/beast/pull/1544/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC96bGliL2RldGFpbC9kZWZsYXRlX3N0cmVhbS5pcHA=) | `80.2% <0%> (+0.1%)` | :arrow_up: |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/beast/pull/1544?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/beast/pull/1544?src=pr&el=footer). Last update [2c141cd...5b2f673](https://codecov.io/gh/boostorg/beast/pull/1544?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---
