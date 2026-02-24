# #1754 Fix async_close error code when async_read times out [Closed]

> Username: reddwarf69  
> Created at: 2019-11-01 20:58:05 UTC  
> Updated at: 2019-11-08 16:55:43 UTC  
> Closed at: 2019-11-06 12:54:04 UTC  
> Url: https://github.com/boostorg/beast/pull/1754  

My understanding of the Beast codebase is limited, but AFAIK this works fine. The rationale is explained in https://github.com/boostorg/beast/issues/1729#issuecomment-547392533.

---

## Comment 1

> Username: vinniefalco  
> Created_at: 2019-11-01 20:59:07 UTC  
> Url: https://github.com/boostorg/beast/pull/1754#issuecomment-548948791  

seems legit, is there a test?

---

## Comment 2

> Username: reddwarf69  
> Created_at: 2019-11-01 21:07:14 UTC  
> Url: https://github.com/boostorg/beast/pull/1754#issuecomment-548951160  

Actually I pushed the wrong code and the example from 1729 now still doesn't work... sorry, I pushed in a rush because I have to go, will try to fix it tomorrow.

---

## Comment 3

> Username: reddwarf69  
> Created_at: 2019-11-01 23:32:48 UTC  
> Url: https://github.com/boostorg/beast/pull/1754#issuecomment-548983607  

It was actually working, but I was building the test manually against the wrong Beast version.  
Anyway, I have also added an unit test now.

---

## Comment 4

> Username: codecov[bot]  
> Created_at: 2019-11-01 23:46:10 UTC  
> Url: https://github.com/boostorg/beast/pull/1754#issuecomment-548985698  

# [Codecov](https://codecov.io/gh/boostorg/beast/pull/1754?src=pr&el=h1) Report  
> Merging [#1754](https://codecov.io/gh/boostorg/beast/pull/1754?src=pr&el=desc) into [develop](https://codecov.io/gh/boostorg/beast/commit/bb85859e96d2815c0a49701733ce4d15f7b156d0?src=pr&el=desc) will **decrease** coverage by `<.01%`.  
> The diff coverage is `100%`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/beast/pull/1754/graphs/tree.svg?width=650&token=Gq6MFA9JRF&height=150&src=pr)](https://codecov.io/gh/boostorg/beast/pull/1754?src=pr&el=tree)  
  
```diff  
@@             Coverage Diff             @@  
##           develop    #1754      +/-   ##  
===========================================  
- Coverage    95.13%   95.12%   -0.01%       
===========================================  
  Files          156      156                
  Lines        11943    11945       +2       
===========================================  
+ Hits         11362    11363       +1       
- Misses         581      582       +1  
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/beast/pull/1754?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [include/boost/beast/websocket/impl/stream\_impl.hpp](https://codecov.io/gh/boostorg/beast/pull/1754/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC93ZWJzb2NrZXQvaW1wbC9zdHJlYW1faW1wbC5ocHA=) | `96.88% <100%> (+0.01%)` | :arrow_up: |  
| [include/boost/beast/zlib/detail/deflate\_stream.ipp](https://codecov.io/gh/boostorg/beast/pull/1754/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC96bGliL2RldGFpbC9kZWZsYXRlX3N0cmVhbS5pcHA=) | `88.09% <0%> (-0.11%)` | :arrow_down: |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/beast/pull/1754?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/beast/pull/1754?src=pr&el=footer). Last update [bb85859...d3c5edd](https://codecov.io/gh/boostorg/beast/pull/1754?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---
