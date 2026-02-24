# #809 Version 122 [Merged]

> Username: vinniefalco  
> Created at: 2017-10-06 18:41:35 UTC  
> Updated at: 2017-10-23 13:05:50 UTC  
> Merged at: 2017-10-11 17:58:25 UTC  
> Closed at: 2017-10-11 17:58:25 UTC  
> Url: https://github.com/boostorg/beast/pull/809  

* Add test for issue 807  
* Fiz zlib symbol conflicts  
* assert on empty buffer in websocket read

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2017-10-07 20:53:57 UTC  
> Updated_at: 2017-10-10 16:05:13 UTC  
> Url: https://github.com/boostorg/beast/pull/809#issuecomment-334965070  

# [Codecov](https://codecov.io/gh/boostorg/beast/pull/809?src=pr&el=h1) Report  
> Merging [#809](https://codecov.io/gh/boostorg/beast/pull/809?src=pr&el=desc) into [develop](https://codecov.io/gh/boostorg/beast/commit/0bc2a414043f4a9430e71e6664d592e67ea6d282?src=pr&el=desc) will **increase** coverage by `0.01%`.  
> The diff coverage is `77.77%`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/beast/pull/809/graphs/tree.svg?width=650&token=Gq6MFA9JRF&height=150&src=pr)](https://codecov.io/gh/boostorg/beast/pull/809?src=pr&el=tree)  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #809      +/-   ##  
===========================================  
+ Coverage    95.71%   95.72%   +0.01%       
===========================================  
  Files          105      105                
  Lines        10547    10550       +3       
===========================================  
+ Hits         10095    10099       +4       
+ Misses         452      451       -1  
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/beast/pull/809?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [include/boost/beast/zlib/inflate\_stream.hpp](https://codecov.io/gh/boostorg/beast/pull/809?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC96bGliL2luZmxhdGVfc3RyZWFtLmhwcA==) | `100% <ø> (ø)` | :arrow_up: |  
| [include/boost/beast/core/string\_param.hpp](https://codecov.io/gh/boostorg/beast/pull/809?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9jb3JlL3N0cmluZ19wYXJhbS5ocHA=) | `100% <ø> (ø)` | :arrow_up: |  
| [include/boost/beast/core/async\_result.hpp](https://codecov.io/gh/boostorg/beast/pull/809?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9jb3JlL2FzeW5jX3Jlc3VsdC5ocHA=) | `100% <ø> (ø)` | :arrow_up: |  
| [include/boost/beast/core/static\_buffer.hpp](https://codecov.io/gh/boostorg/beast/pull/809?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9jb3JlL3N0YXRpY19idWZmZXIuaHBw) | `100% <ø> (ø)` | :arrow_up: |  
| [include/boost/beast/core/buffers\_cat.hpp](https://codecov.io/gh/boostorg/beast/pull/809?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9jb3JlL2J1ZmZlcnNfY2F0LmhwcA==) | `100% <ø> (ø)` | :arrow_up: |  
| [include/boost/beast/core/handler\_ptr.hpp](https://codecov.io/gh/boostorg/beast/pull/809?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9jb3JlL2hhbmRsZXJfcHRyLmhwcA==) | `100% <ø> (ø)` | :arrow_up: |  
| [include/boost/beast/core/bind\_handler.hpp](https://codecov.io/gh/boostorg/beast/pull/809?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9jb3JlL2JpbmRfaGFuZGxlci5ocHA=) | `100% <ø> (ø)` | :arrow_up: |  
| [include/boost/beast/core/multi\_buffer.hpp](https://codecov.io/gh/boostorg/beast/pull/809?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9jb3JlL211bHRpX2J1ZmZlci5ocHA=) | `100% <ø> (ø)` | :arrow_up: |  
| [include/boost/beast/websocket/stream.hpp](https://codecov.io/gh/boostorg/beast/pull/809?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC93ZWJzb2NrZXQvc3RyZWFtLmhwcA==) | `100% <ø> (ø)` | :arrow_up: |  
| [include/boost/beast/core/handler\_alloc.hpp](https://codecov.io/gh/boostorg/beast/pull/809?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9jb3JlL2hhbmRsZXJfYWxsb2MuaHBw) | `100% <ø> (ø)` | :arrow_up: |  
| ... and [33 more](https://codecov.io/gh/boostorg/beast/pull/809?src=pr&el=tree-more) | |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/beast/pull/809?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/beast/pull/809?src=pr&el=footer). Last update [0bc2a41...f09b2d3](https://codecov.io/gh/boostorg/beast/pull/809?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---
