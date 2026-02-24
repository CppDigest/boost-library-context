# #1255 beast: misc-typos [Closed]

> Username: luzpaz  
> Created at: 2018-09-23 18:58:22 UTC  
> Updated at: 2018-10-07 18:28:17 UTC  
> Closed at: 2018-10-07 18:28:17 UTC  
> Url: https://github.com/boostorg/beast/pull/1255  

Found via `codespell -q 3 -L uint,te,tim --skip="./test/extern/zlib*,./example/http/client/crawl*"`

---

## Comment 1

> Username: vinniefalco  
> Created_at: 2018-09-23 21:26:54 UTC  
> Url: https://github.com/boostorg/beast/pull/1255#issuecomment-423849206  

Good finds! One little detail though, the files http_parser.c and http_parser.h should be left alone since they are external code and it is important that the source files are exactly the same as they are in the foreign repository, even with the spelling errors.

---

## Comment 2

> Username: luzpaz  
> Created_at: 2018-09-23 21:37:03 UTC  
> Url: https://github.com/boostorg/beast/pull/1255#issuecomment-423849769  

@vinniefalco oops...tried to avoid changing any 3rd party code. Reverted in 1e96ee0

---

## Comment 3

> Username: codecov[bot]  
> Created_at: 2018-09-24 02:49:07 UTC  
> Url: https://github.com/boostorg/beast/pull/1255#issuecomment-423867862  

# [Codecov](https://codecov.io/gh/boostorg/beast/pull/1255?src=pr&el=h1) Report  
> Merging [#1255](https://codecov.io/gh/boostorg/beast/pull/1255?src=pr&el=desc) into [develop](https://codecov.io/gh/boostorg/beast/commit/f2c3b5dec163ed2b14c5b0e86b63048f1b5a74ff?src=pr&el=desc) will **not change** coverage.  
> The diff coverage is `n/a`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/beast/pull/1255/graphs/tree.svg?width=650&token=Gq6MFA9JRF&height=150&src=pr)](https://codecov.io/gh/boostorg/beast/pull/1255?src=pr&el=tree)  
  
```diff  
@@           Coverage Diff            @@  
##           develop    #1255   +/-   ##  
========================================  
  Coverage    95.52%   95.52%             
========================================  
  Files          113      113             
  Lines        11064    11064             
========================================  
  Hits         10569    10569             
  Misses         495      495  
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/beast/pull/1255?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [include/boost/beast/http/span\_body.hpp](https://codecov.io/gh/boostorg/beast/pull/1255/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9odHRwL3NwYW5fYm9keS5ocHA=) | `93.93% <ø> (ø)` | :arrow_up: |  
| [include/boost/beast/core/impl/static\_buffer.ipp](https://codecov.io/gh/boostorg/beast/pull/1255/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9jb3JlL2ltcGwvc3RhdGljX2J1ZmZlci5pcHA=) | `96.15% <ø> (ø)` | :arrow_up: |  
| [include/boost/beast/websocket/stream.hpp](https://codecov.io/gh/boostorg/beast/pull/1255/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC93ZWJzb2NrZXQvc3RyZWFtLmhwcA==) | `100% <ø> (ø)` | :arrow_up: |  
| [include/boost/beast/core/static\_buffer.hpp](https://codecov.io/gh/boostorg/beast/pull/1255/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9jb3JlL3N0YXRpY19idWZmZXIuaHBw) | `100% <ø> (ø)` | :arrow_up: |  
| [include/boost/beast/http/basic\_file\_body.hpp](https://codecov.io/gh/boostorg/beast/pull/1255/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9odHRwL2Jhc2ljX2ZpbGVfYm9keS5ocHA=) | `81.96% <ø> (ø)` | :arrow_up: |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/beast/pull/1255?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/beast/pull/1255?src=pr&el=footer). Last update [f2c3b5d...1e96ee0](https://codecov.io/gh/boostorg/beast/pull/1255?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---
