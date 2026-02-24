# #1583 Silence gcc-8 warning: [Closed]

> Username: djarek  
> Created at: 2019-04-20 23:01:20 UTC  
> Updated at: 2019-09-10 21:04:13 UTC  
> Closed at: 2019-05-04 16:31:35 UTC  
> Url: https://github.com/boostorg/beast/pull/1583  

The warning was caused by a false positive triggered by boost::optional.  
Reference:  
https://github.com/boostorg/optional/issues/72

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2019-04-21 03:22:57 UTC  
> Url: https://github.com/boostorg/beast/pull/1583#issuecomment-485221404  

# [Codecov](https://codecov.io/gh/boostorg/beast/pull/1583?src=pr&el=h1) Report  
> Merging [#1583](https://codecov.io/gh/boostorg/beast/pull/1583?src=pr&el=desc) into [develop](https://codecov.io/gh/boostorg/beast/commit/41040a21f0b0e12537beb1262c2d538a023efe65?src=pr&el=desc) will **decrease** coverage by `<.01%`.  
> The diff coverage is `100%`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/beast/pull/1583/graphs/tree.svg?width=650&token=Gq6MFA9JRF&height=150&src=pr)](https://codecov.io/gh/boostorg/beast/pull/1583?src=pr&el=tree)  
  
```diff  
@@            Coverage Diff             @@  
##           develop   #1583      +/-   ##  
==========================================  
- Coverage     92.6%   92.6%   -0.01%       
==========================================  
  Files          151     151                
  Lines        12131   12130       -1       
==========================================  
- Hits         11234   11233       -1       
  Misses         897     897  
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/beast/pull/1583?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [include/boost/beast/websocket/impl/read.hpp](https://codecov.io/gh/boostorg/beast/pull/1583/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC93ZWJzb2NrZXQvaW1wbC9yZWFkLmhwcA==) | `86.49% <100%> (-0.03%)` | :arrow_down: |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/beast/pull/1583?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/beast/pull/1583?src=pr&el=footer). Last update [41040a2...479aa4e](https://codecov.io/gh/boostorg/beast/pull/1583?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---
