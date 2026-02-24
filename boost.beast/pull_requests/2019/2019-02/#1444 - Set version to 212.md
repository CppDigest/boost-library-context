# #1444 Set version to 212 [Closed]

> Username: vinniefalco  
> Created at: 2019-02-09 05:10:52 UTC  
> Updated at: 2019-02-17 16:37:36 UTC  
> Closed at: 2019-02-10 13:52:16 UTC  
> Url: https://github.com/boostorg/beast/pull/1444  

* dynamic_buffer_ref tests and tidy  
* flat_stream tests and tidy  
* stranded_socket tests and tidy  
* buffers_front tests  
* Improved websocket stream documentation

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2019-02-09 06:55:31 UTC  
> Updated_at: 2019-02-09 16:03:34 UTC  
> Url: https://github.com/boostorg/beast/pull/1444#issuecomment-462020005  

# [Codecov](https://codecov.io/gh/boostorg/beast/pull/1444?src=pr&el=h1) Report  
> Merging [#1444](https://codecov.io/gh/boostorg/beast/pull/1444?src=pr&el=desc) into [develop](https://codecov.io/gh/boostorg/beast/commit/06efddd8b851610b5b3a5832ac87f1c52b838d9b?src=pr&el=desc) will **increase** coverage by `0.72%`.  
> The diff coverage is `100%`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/beast/pull/1444/graphs/tree.svg?width=650&token=Gq6MFA9JRF&height=150&src=pr)](https://codecov.io/gh/boostorg/beast/pull/1444?src=pr&el=tree)  
  
```diff  
@@             Coverage Diff             @@  
##           develop    #1444      +/-   ##  
===========================================  
+ Coverage    93.29%   94.02%   +0.72%       
===========================================  
  Files          135      135                
  Lines        11590    11642      +52       
===========================================  
+ Hits         10813    10946     +133       
+ Misses         777      696      -81  
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/beast/pull/1444?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [include/boost/beast/core/dynamic\_buffer\_ref.hpp](https://codecov.io/gh/boostorg/beast/pull/1444/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9jb3JlL2R5bmFtaWNfYnVmZmVyX3JlZi5ocHA=) | `100% <ø> (+100%)` | :arrow_up: |  
| [include/boost/beast/websocket/stream.hpp](https://codecov.io/gh/boostorg/beast/pull/1444/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC93ZWJzb2NrZXQvc3RyZWFtLmhwcA==) | `100% <ø> (ø)` | :arrow_up: |  
| [include/boost/beast/core/basic\_timeout\_stream.hpp](https://codecov.io/gh/boostorg/beast/pull/1444/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9jb3JlL2Jhc2ljX3RpbWVvdXRfc3RyZWFtLmhwcA==) | `100% <ø> (ø)` | :arrow_up: |  
| [include/boost/beast/core/flat\_stream.hpp](https://codecov.io/gh/boostorg/beast/pull/1444/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9jb3JlL2ZsYXRfc3RyZWFtLmhwcA==) | `100% <ø> (ø)` | :arrow_up: |  
| [include/boost/beast/core/detail/flat\_stream.hpp](https://codecov.io/gh/boostorg/beast/pull/1444/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9jb3JlL2RldGFpbC9mbGF0X3N0cmVhbS5ocHA=) | `100% <100%> (ø)` | :arrow_up: |  
| [include/boost/beast/core/stranded\_socket.hpp](https://codecov.io/gh/boostorg/beast/pull/1444/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9jb3JlL3N0cmFuZGVkX3NvY2tldC5ocHA=) | `100% <100%> (ø)` | |  
| [...nclude/boost/beast/core/detail/stranded\_socket.hpp](https://codecov.io/gh/boostorg/beast/pull/1444/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9jb3JlL2RldGFpbC9zdHJhbmRlZF9zb2NrZXQuaHBw) | `100% <100%> (ø)` | |  
| [include/boost/beast/core/impl/flat\_stream.hpp](https://codecov.io/gh/boostorg/beast/pull/1444/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9jb3JlL2ltcGwvZmxhdF9zdHJlYW0uaHBw) | `100% <100%> (+26.86%)` | :arrow_up: |  
| [...ude/boost/beast/core/impl/basic\_timeout\_stream.hpp](https://codecov.io/gh/boostorg/beast/pull/1444/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9jb3JlL2ltcGwvYmFzaWNfdGltZW91dF9zdHJlYW0uaHBw) | `97.97% <0%> (-0.51%)` | :arrow_down: |  
| ... and [5 more](https://codecov.io/gh/boostorg/beast/pull/1444/diff?src=pr&el=tree-more) | |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/beast/pull/1444?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/beast/pull/1444?src=pr&el=footer). Last update [06efddd...1af1340](https://codecov.io/gh/boostorg/beast/pull/1444?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---
