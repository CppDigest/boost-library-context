# #1237 add certificate verifying in ssl client examples [Closed]

> Username: ghost  
> Created at: 2018-08-28 02:43:56 UTC  
> Updated at: 2018-09-24 02:37:31 UTC  
> Closed at: 2018-09-24 02:37:31 UTC  
> Url: https://github.com/boostorg/beast/pull/1237  

Verify the remote servers' certificates in the ssl examples.

---

## Comment 1

> Username: vinniefalco  
> Created_at: 2018-08-30 20:13:04 UTC  
> Updated_at: 2018-08-30 20:13:24 UTC  
> Url: https://github.com/boostorg/beast/pull/1237#issuecomment-417452091  

Why are removing that one certificate? Also what about the websocket clients?

---

## Comment 2

> Username: ghost  
> Created_at: 2018-08-30 21:23:52 UTC  
> Url: https://github.com/boostorg/beast/pull/1237#issuecomment-417471900  

Here should be a loop, add one authority once.

---

## Comment 3

> Username: codecov[bot]  
> Created_at: 2018-08-30 21:30:11 UTC  
> Url: https://github.com/boostorg/beast/pull/1237#issuecomment-417473464  

# [Codecov](https://codecov.io/gh/boostorg/beast/pull/1237?src=pr&el=h1) Report  
> Merging [#1237](https://codecov.io/gh/boostorg/beast/pull/1237?src=pr&el=desc) into [develop](https://codecov.io/gh/boostorg/beast/commit/1da229a27c6f0539d422bcedbcf47cfe2517164a?src=pr&el=desc) will **decrease** coverage by `<.01%`.  
> The diff coverage is `n/a`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/beast/pull/1237/graphs/tree.svg?width=650&token=Gq6MFA9JRF&height=150&src=pr)](https://codecov.io/gh/boostorg/beast/pull/1237?src=pr&el=tree)  
  
```diff  
@@             Coverage Diff             @@  
##           develop    #1237      +/-   ##  
===========================================  
- Coverage    95.52%   95.51%   -0.01%       
===========================================  
  Files          113      113                
  Lines        11064    11064                
===========================================  
- Hits         10569    10568       -1       
- Misses         495      496       +1  
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/beast/pull/1237?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [include/boost/beast/websocket/impl/close.ipp](https://codecov.io/gh/boostorg/beast/pull/1237/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC93ZWJzb2NrZXQvaW1wbC9jbG9zZS5pcHA=) | `99.49% <0%> (-0.51%)` | :arrow_down: |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/beast/pull/1237?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/beast/pull/1237?src=pr&el=footer). Last update [1da229a...9450271](https://codecov.io/gh/boostorg/beast/pull/1237?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---
