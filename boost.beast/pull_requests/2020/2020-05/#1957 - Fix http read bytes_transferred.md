# #1957 Fix http read bytes_transferred [Closed]

> Username: madmongo1  
> Created at: 2020-05-19 20:52:17 UTC  
> Updated at: 2020-05-20 14:18:33 UTC  
> Closed at: 2020-05-20 14:18:33 UTC  
> Url: https://github.com/boostorg/beast/pull/1957  

fixes #1942

---

## Review 1 [Commented]

> Username: vinniefalco  
> Created_at: 2020-05-19 20:59:38 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/1957#pullrequestreview-414818931  

📁 include/boost/beast/http/impl/read.hpp

```diff
 246 |+     DynamicBuffer& b_;
 247 |+     basic_parser<isRequest>& p_;
 248 |+     std::size_t bytes_transferred_;
```

> Username: vinniefalco  
> Created_at: 2020-05-19 20:59:38 UTC  
> Url: https://github.com/boostorg/beast/pull/1957#discussion_r427596546  

Why didn't you just initialize this in-class?

> Username: madmongo1  
> Created_at: 2020-05-19 21:04:49 UTC  
> Url: https://github.com/boostorg/beast/pull/1957#discussion_r427599179  

Old habits die hard...


---

## Comment 2

> Username: codecov[bot]  
> Created_at: 2020-05-19 21:05:31 UTC  
> Url: https://github.com/boostorg/beast/pull/1957#issuecomment-631082025  

# [Codecov](https://codecov.io/gh/boostorg/beast/pull/1957?src=pr&el=h1) Report  
> Merging [#1957](https://codecov.io/gh/boostorg/beast/pull/1957?src=pr&el=desc) into [develop](https://codecov.io/gh/boostorg/beast/commit/fc7c645f64192976acfb6a88a8fdbd5d92bc44fc&el=desc) will **decrease** coverage by `0.00%`.  
> The diff coverage is `98.27%`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/beast/pull/1957/graphs/tree.svg?width=650&height=150&src=pr&token=Gq6MFA9JRF)](https://codecov.io/gh/boostorg/beast/pull/1957?src=pr&el=tree)  
  
```diff  
@@             Coverage Diff             @@  
##           develop    #1957      +/-   ##  
===========================================  
- Coverage    95.11%   95.10%   -0.01%       
===========================================  
  Files          156      155       -1       
  Lines        12032    12055      +23       
===========================================  
+ Hits         11444    11465      +21       
- Misses         588      590       +2       
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/beast/pull/1957?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [include/boost/beast/http/impl/read.hpp](https://codecov.io/gh/boostorg/beast/pull/1957/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9odHRwL2ltcGwvcmVhZC5ocHA=) | `98.79% <98.27%> (-0.23%)` | :arrow_down: |  
| [include/boost/beast/zlib/detail/inflate\_stream.ipp](https://codecov.io/gh/boostorg/beast/pull/1957/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC96bGliL2RldGFpbC9pbmZsYXRlX3N0cmVhbS5pcHA=) | `94.37% <0.00%> (-0.20%)` | :arrow_down: |  
| [include/boost/beast/core/detail/impl/read.hpp](https://codecov.io/gh/boostorg/beast/pull/1957/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9jb3JlL2RldGFpbC9pbXBsL3JlYWQuaHBw) | | |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/beast/pull/1957?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/beast/pull/1957?src=pr&el=footer). Last update [fc7c645...6b18ff6](https://codecov.io/gh/boostorg/beast/pull/1957?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---
