# #1344 Don't collect coverage of experimental code [Closed]

> Username: djarek  
> Created at: 2018-12-03 15:24:43 UTC  
> Updated at: 2019-09-10 21:03:40 UTC  
> Closed at: 2018-12-04 00:38:36 UTC  
> Url: https://github.com/boostorg/beast/pull/1344  

Code from beast/_experimental will be ignored when collecting coverage.

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2018-12-03 17:33:48 UTC  
> Updated_at: 2018-12-03 21:21:08 UTC  
> Url: https://github.com/boostorg/beast/pull/1344#issuecomment-443797204  

# [Codecov](https://codecov.io/gh/boostorg/beast/pull/1344?src=pr&el=h1) Report  
> Merging [#1344](https://codecov.io/gh/boostorg/beast/pull/1344?src=pr&el=desc) into [develop](https://codecov.io/gh/boostorg/beast/commit/397c388e18415798847cbcc574ad8ff95633a233?src=pr&el=desc) will **increase** coverage by `0.7%`.  
> The diff coverage is `n/a`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/beast/pull/1344/graphs/tree.svg?width=650&token=Gq6MFA9JRF&height=150&src=pr)](https://codecov.io/gh/boostorg/beast/pull/1344?src=pr&el=tree)  
  
```diff  
@@            Coverage Diff             @@  
##           develop    #1344     +/-   ##  
==========================================  
+ Coverage    92.06%   92.76%   +0.7%       
==========================================  
  Files          124      109     -15       
  Lines        11526    10885    -641       
==========================================  
- Hits         10611    10098    -513       
+ Misses         915      787    -128  
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/beast/pull/1344?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [include/boost/beast/\_experimental/test/stream.hpp](https://codecov.io/gh/boostorg/beast/pull/1344/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9fZXhwZXJpbWVudGFsL3Rlc3Qvc3RyZWFtLmhwcA==) | | |  
| [...lude/boost/beast/\_experimental/unit\_test/match.hpp](https://codecov.io/gh/boostorg/beast/pull/1344/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9fZXhwZXJpbWVudGFsL3VuaXRfdGVzdC9tYXRjaC5ocHA=) | | |  
| [...lude/boost/beast/\_experimental/unit\_test/suite.hpp](https://codecov.io/gh/boostorg/beast/pull/1344/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9fZXhwZXJpbWVudGFsL3VuaXRfdGVzdC9zdWl0ZS5ocHA=) | | |  
| [...boost/beast/\_experimental/unit\_test/suite\_info.hpp](https://codecov.io/gh/boostorg/beast/pull/1344/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9fZXhwZXJpbWVudGFsL3VuaXRfdGVzdC9zdWl0ZV9pbmZvLmhwcA==) | | |  
| [...boost/beast/\_experimental/unit\_test/suite\_list.hpp](https://codecov.io/gh/boostorg/beast/pull/1344/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9fZXhwZXJpbWVudGFsL3VuaXRfdGVzdC9zdWl0ZV9saXN0LmhwcA==) | | |  
| [...lude/boost/beast/\_experimental/test/impl/error.ipp](https://codecov.io/gh/boostorg/beast/pull/1344/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9fZXhwZXJpbWVudGFsL3Rlc3QvaW1wbC9lcnJvci5pcHA=) | | |  
| [...ude/boost/beast/\_experimental/unit\_test/runner.hpp](https://codecov.io/gh/boostorg/beast/pull/1344/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9fZXhwZXJpbWVudGFsL3VuaXRfdGVzdC9ydW5uZXIuaHBw) | | |  
| [...lude/boost/beast/\_experimental/test/fail\_count.hpp](https://codecov.io/gh/boostorg/beast/pull/1344/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9fZXhwZXJpbWVudGFsL3Rlc3QvZmFpbF9jb3VudC5ocHA=) | | |  
| [...boost/beast/\_experimental/test/impl/fail\_count.ipp](https://codecov.io/gh/boostorg/beast/pull/1344/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9fZXhwZXJpbWVudGFsL3Rlc3QvaW1wbC9mYWlsX2NvdW50LmlwcA==) | | |  
| [...\_experimental/unit\_test/detail/const\_container.hpp](https://codecov.io/gh/boostorg/beast/pull/1344/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9fZXhwZXJpbWVudGFsL3VuaXRfdGVzdC9kZXRhaWwvY29uc3RfY29udGFpbmVyLmhwcA==) | | |  
| ... and [5 more](https://codecov.io/gh/boostorg/beast/pull/1344/diff?src=pr&el=tree-more) | |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/beast/pull/1344?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/beast/pull/1344?src=pr&el=footer). Last update [397c388...e708343](https://codecov.io/gh/boostorg/beast/pull/1344?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---
