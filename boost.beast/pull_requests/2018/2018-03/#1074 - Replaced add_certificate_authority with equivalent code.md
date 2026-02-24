# #1074 Replaced add_certificate_authority with equivalent code [Closed]

> Username: bsergeev  
> Created at: 2018-03-19 22:39:50 UTC  
> Updated at: 2018-05-08 22:43:36 UTC  
> Closed at: 2018-05-08 22:40:51 UTC  
> Url: https://github.com/boostorg/beast/pull/1074  

The replacement code handles multiple certificates in the memory buffer, which used to be passed to `boost::asio::ssl::context::add_certificate_authority()`.  
If/when my PR for ASIO, https://github.com/chriskohlhoff/asio/pull/294, makes it into Boost ASIO, this code may be removed and call `add_certificate_authority()` restored.

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2018-03-19 23:32:31 UTC  
> Url: https://github.com/boostorg/beast/pull/1074#issuecomment-374421170  

# [Codecov](https://codecov.io/gh/boostorg/beast/pull/1074?src=pr&el=h1) Report  
> Merging [#1074](https://codecov.io/gh/boostorg/beast/pull/1074?src=pr&el=desc) into [develop](https://codecov.io/gh/boostorg/beast/commit/4d660a5e54dee428e99ec0520620e028c1975727?src=pr&el=desc) will **decrease** coverage by `<.01%`.  
> The diff coverage is `n/a`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/beast/pull/1074/graphs/tree.svg?width=650&src=pr&token=Gq6MFA9JRF&height=150)](https://codecov.io/gh/boostorg/beast/pull/1074?src=pr&el=tree)  
  
```diff  
@@             Coverage Diff             @@  
##           develop    #1074      +/-   ##  
===========================================  
- Coverage    95.52%   95.51%   -0.01%       
===========================================  
  Files          106      106                
  Lines        10627    10627                
===========================================  
- Hits         10151    10150       -1       
- Misses         476      477       +1  
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/beast/pull/1074?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [include/boost/beast/zlib/detail/deflate\_stream.hpp](https://codecov.io/gh/boostorg/beast/pull/1074/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC96bGliL2RldGFpbC9kZWZsYXRlX3N0cmVhbS5ocHA=) | `92.32% <0%> (-0.11%)` | :arrow_down: |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/beast/pull/1074?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/beast/pull/1074?src=pr&el=footer). Last update [4d660a5...949859e](https://codecov.io/gh/boostorg/beast/pull/1074?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---

## Comment 2

> Username: stale[bot]  
> Created_at: 2018-04-24 16:52:17 UTC  
> Url: https://github.com/boostorg/beast/pull/1074#issuecomment-384003455  

This issue has been open for a while with no activity, has it been resolved?

---

## Comment 3

> Username: bsergeev  
> Created_at: 2018-04-24 19:43:06 UTC  
> Url: https://github.com/boostorg/beast/pull/1074#issuecomment-384055635  

Since ASIO accepted my PR with the fix to `boost::asio::ssl::context::add_certificate_authority()` , this workaround won't be necessary, when updated Boost ASIO is released.

---

## Comment 4

> Username: vinniefalco  
> Created_at: 2018-05-08 22:40:49 UTC  
> Url: https://github.com/boostorg/beast/pull/1074#issuecomment-387565009  

Thanks for pointing this out. I think we will just wait for the updated Asio, because fiddling with the certificates code is something I don't like to do. I'm not entirely happy with Beast's certificate code anyway since it doesn't work in all cases (it doesn't respect certificates in the operating system's root certificate store).

---

## Comment 5

> Username: bsergeev  
> Created_at: 2018-05-08 22:43:14 UTC  
> Updated_at: 2018-05-08 22:43:36 UTC  
> Url: https://github.com/boostorg/beast/pull/1074#issuecomment-387565515  

ASIO in Boost 1.67 includes my PR with the fix, so, no changes in Beast are needed!

---
