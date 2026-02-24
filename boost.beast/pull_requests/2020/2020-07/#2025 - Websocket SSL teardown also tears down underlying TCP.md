# #2025 Websocket SSL teardown also tears down underlying TCP [Closed]

> Username: madmongo1  
> Created at: 2020-07-20 10:40:56 UTC  
> Updated at: 2020-08-29 09:42:20 UTC  
> Closed at: 2020-08-29 09:42:20 UTC  
> Url: https://github.com/boostorg/beast/pull/2025  

fixes #2023

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2020-07-20 13:54:55 UTC  
> Updated_at: 2020-07-20 14:03:58 UTC  
> Url: https://github.com/boostorg/beast/pull/2025#issuecomment-661054857  

# [Codecov](https://codecov.io/gh/boostorg/beast/pull/2025?src=pr&el=h1) Report  
> Merging [#2025](https://codecov.io/gh/boostorg/beast/pull/2025?src=pr&el=desc) into [develop](https://codecov.io/gh/boostorg/beast/commit/3486e9cb18aa39b392e07031a33e65b1792fbccf&el=desc) will **decrease** coverage by `0.01%`.  
> The diff coverage is `0.00%`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/beast/pull/2025/graphs/tree.svg?width=650&height=150&src=pr&token=Gq6MFA9JRF)](https://codecov.io/gh/boostorg/beast/pull/2025?src=pr&el=tree)  
  
```diff  
@@             Coverage Diff             @@  
##           develop    #2025      +/-   ##  
===========================================  
- Coverage    95.09%   95.07%   -0.02%       
===========================================  
  Files          153      153                
  Lines        11939    11941       +2       
===========================================  
  Hits         11353    11353                
- Misses         586      588       +2       
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/beast/pull/2025?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [include/boost/beast/websocket/impl/ssl.hpp](https://codecov.io/gh/boostorg/beast/pull/2025/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC93ZWJzb2NrZXQvaW1wbC9zc2wuaHBw) | `0.00% <0.00%> (ø)` | |  
| [include/boost/beast/websocket/impl/ping.hpp](https://codecov.io/gh/boostorg/beast/pull/2025/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC93ZWJzb2NrZXQvaW1wbC9waW5nLmhwcA==) | `96.42% <0.00%> (-0.90%)` | :arrow_down: |  
| [include/boost/beast/zlib/detail/inflate\_stream.ipp](https://codecov.io/gh/boostorg/beast/pull/2025/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC96bGliL2RldGFpbC9pbmZsYXRlX3N0cmVhbS5pcHA=) | `94.57% <0.00%> (+0.19%)` | :arrow_up: |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/beast/pull/2025?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/beast/pull/2025?src=pr&el=footer). Last update [3486e9c...65f2152](https://codecov.io/gh/boostorg/beast/pull/2025?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---

## Comment 2

> Username: stale[bot]  
> Created_at: 2020-08-22 19:00:33 UTC  
> Url: https://github.com/boostorg/beast/pull/2025#issuecomment-678678907  

This issue has been open for a while with no activity, has it been resolved?

---

## Comment 3

> Username: madmongo1  
> Created_at: 2020-08-29 09:42:20 UTC  
> Url: https://github.com/boostorg/beast/pull/2025#issuecomment-683265958  

Merged with #2037

---
