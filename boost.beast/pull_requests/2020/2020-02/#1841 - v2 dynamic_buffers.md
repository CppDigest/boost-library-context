# #1841 v2 dynamic_buffers [Closed]

> Username: madmongo1  
> Created at: 2020-02-07 15:06:14 UTC  
> Updated at: 2020-03-03 15:47:32 UTC  
> Closed at: 2020-03-02 21:07:06 UTC  
> Url: https://github.com/boostorg/beast/pull/1841  

NOT A PULL REQUEST

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2020-02-07 15:18:39 UTC  
> Updated_at: 2020-03-02 20:08:18 UTC  
> Url: https://github.com/boostorg/beast/pull/1841#issuecomment-583447894  

# [Codecov](https://codecov.io/gh/boostorg/beast/pull/1841?src=pr&el=h1) Report  
> Merging [#1841](https://codecov.io/gh/boostorg/beast/pull/1841?src=pr&el=desc) into [develop](https://codecov.io/gh/boostorg/beast/commit/c8a726f962b2fbf77d00b273b3c6fb0dd975a6b5?src=pr&el=desc) will **decrease** coverage by `0.32%`.  
> The diff coverage is `76.15%`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/beast/pull/1841/graphs/tree.svg?width=650&token=Gq6MFA9JRF&height=150&src=pr)](https://codecov.io/gh/boostorg/beast/pull/1841?src=pr&el=tree)  
  
```diff  
@@             Coverage Diff             @@  
##           develop    #1841      +/-   ##  
===========================================  
- Coverage    95.09%   94.76%   -0.33%       
===========================================  
  Files          156      158       +2       
  Lines        12011    12234     +223       
===========================================  
+ Hits         11422    11594     +172       
- Misses         589      640      +51  
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/beast/pull/1841?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [include/boost/beast/core/impl/multi\_buffer.hpp](https://codecov.io/gh/boostorg/beast/pull/1841/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9jb3JlL2ltcGwvbXVsdGlfYnVmZmVyLmhwcA==) | `98.96% <ø> (+0.82%)` | :arrow_up: |  
| [include/boost/beast/core/flat\_static\_buffer.hpp](https://codecov.io/gh/boostorg/beast/pull/1841/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9jb3JlL2ZsYXRfc3RhdGljX2J1ZmZlci5ocHA=) | `100% <ø> (ø)` | :arrow_up: |  
| [include/boost/beast/core/flat\_buffer.hpp](https://codecov.io/gh/boostorg/beast/pull/1841/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9jb3JlL2ZsYXRfYnVmZmVyLmhwcA==) | `100% <ø> (ø)` | :arrow_up: |  
| [include/boost/beast/core/static\_buffer.hpp](https://codecov.io/gh/boostorg/beast/pull/1841/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9jb3JlL3N0YXRpY19idWZmZXIuaHBw) | `100% <ø> (ø)` | :arrow_up: |  
| [include/boost/beast/core/dynamic\_buffer.hpp](https://codecov.io/gh/boostorg/beast/pull/1841/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9jb3JlL2R5bmFtaWNfYnVmZmVyLmhwcA==) | `100% <100%> (ø)` | |  
| [include/boost/beast/core/impl/read\_size.hpp](https://codecov.io/gh/boostorg/beast/pull/1841/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9jb3JlL2ltcGwvcmVhZF9zaXplLmhwcA==) | `100% <100%> (ø)` | :arrow_up: |  
| [...lude/boost/beast/core/detail/dynamic\_buffer\_v0.hpp](https://codecov.io/gh/boostorg/beast/pull/1841/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9jb3JlL2RldGFpbC9keW5hbWljX2J1ZmZlcl92MC5ocHA=) | `100% <100%> (ø)` | |  
| [include/boost/beast/core/detail/impl/read.hpp](https://codecov.io/gh/boostorg/beast/pull/1841/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9jb3JlL2RldGFpbC9pbXBsL3JlYWQuaHBw) | `100% <100%> (ø)` | :arrow_up: |  
| [include/boost/beast/http/impl/read.hpp](https://codecov.io/gh/boostorg/beast/pull/1841/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9odHRwL2ltcGwvcmVhZC5ocHA=) | `99.07% <100%> (+0.05%)` | :arrow_up: |  
| [include/boost/beast/core/impl/buffers\_adaptor.hpp](https://codecov.io/gh/boostorg/beast/pull/1841/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9jb3JlL2ltcGwvYnVmZmVyc19hZGFwdG9yLmhwcA==) | `98.6% <100%> (ø)` | :arrow_up: |  
| ... and [13 more](https://codecov.io/gh/boostorg/beast/pull/1841/diff?src=pr&el=tree-more) | |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/beast/pull/1841?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/beast/pull/1841?src=pr&el=footer). Last update [c8a726f...a15024e](https://codecov.io/gh/boostorg/beast/pull/1841?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---

## Review 2 [Commented]

> Username: vinniefalco  
> Created_at: 2020-02-12 23:05:58 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/1841#pullrequestreview-357853561  

📁 include/boost/beast/core/detail/config.hpp

```diff
 104 |+ #ifndef BOOST_BEAST_V1_DYNAMIC_BUFFER_PROXY
 105 |+ #define BOOST_BEAST_V1_DYNAMIC_BUFFER_PROXY(type) \
 106 |+     typename std::enable_if< \
```

> Username: vinniefalco  
> Created_at: 2020-02-12 23:05:58 UTC  
> Updated_at: 2020-03-02 18:00:38 UTC  
> Url: https://github.com/boostorg/beast/pull/1841#discussion_r378564788  

as long as we are fully qualifying namespaces why not `::std`?

> Username: madmongo1  
> Created_at: 2020-03-03 15:47:31 UTC  
> Updated_at: 2020-03-03 15:47:32 UTC  
> Url: https://github.com/boostorg/beast/pull/1841#discussion_r387110634  

actually, this define can go now. The return type of dynamic_buffer is now a documented template type.


---
