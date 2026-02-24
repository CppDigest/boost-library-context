# #1557 Fix async_base immediate completion [Closed]

> Username: djarek  
> Created at: 2019-04-05 08:57:25 UTC  
> Updated at: 2019-09-10 21:04:05 UTC  
> Closed at: 2019-04-07 20:56:31 UTC  
> Url: https://github.com/boostorg/beast/pull/1557  

`complete(false, ...)` used the wrong executor when an async operation completed immediately, potentially executing the completion handler on the IO executor in some cases.

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2019-04-05 09:14:19 UTC  
> Url: https://github.com/boostorg/beast/pull/1557#issuecomment-480206168  

# [Codecov](https://codecov.io/gh/boostorg/beast/pull/1557?src=pr&el=h1) Report  
> Merging [#1557](https://codecov.io/gh/boostorg/beast/pull/1557?src=pr&el=desc) into [develop](https://codecov.io/gh/boostorg/beast/commit/50d5b965dd1df062ea97fefdbcbc82e428f3eb58?src=pr&el=desc) will **decrease** coverage by `0.03%`.  
> The diff coverage is `100%`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/beast/pull/1557/graphs/tree.svg?width=650&token=Gq6MFA9JRF&height=150&src=pr)](https://codecov.io/gh/boostorg/beast/pull/1557?src=pr&el=tree)  
  
```diff  
@@             Coverage Diff             @@  
##           develop    #1557      +/-   ##  
===========================================  
- Coverage    92.66%   92.63%   -0.04%       
===========================================  
  Files          150      150                
  Lines        12130    12131       +1       
===========================================  
- Hits         11240    11237       -3       
- Misses         890      894       +4  
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/beast/pull/1557?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [include/boost/beast/core/async\_base.hpp](https://codecov.io/gh/boostorg/beast/pull/1557/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9jb3JlL2FzeW5jX2Jhc2UuaHBw) | `100% <100%> (ø)` | :arrow_up: |  
| [include/boost/beast/websocket/impl/read.hpp](https://codecov.io/gh/boostorg/beast/pull/1557/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC93ZWJzb2NrZXQvaW1wbC9yZWFkLmhwcA==) | `86.51% <0%> (-0.71%)` | :arrow_down: |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/beast/pull/1557?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/beast/pull/1557?src=pr&el=footer). Last update [50d5b96...6c6d9f9](https://codecov.io/gh/boostorg/beast/pull/1557?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---

## Comment 2

> Username: djarek  
> Created_at: 2019-04-06 20:39:10 UTC  
> Url: https://github.com/boostorg/beast/pull/1557#issuecomment-480535905  

Already merged.

---
