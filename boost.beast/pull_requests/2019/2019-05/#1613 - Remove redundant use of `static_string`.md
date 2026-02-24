# #1613 Remove redundant use of `static_string` [Closed]

> Username: djarek  
> Created at: 2019-05-19 15:43:42 UTC  
> Updated at: 2019-09-10 21:04:33 UTC  
> Closed at: 2019-05-20 16:49:29 UTC  
> Url: https://github.com/boostorg/beast/pull/1613  

This avoids an instantiation and copy of the data when adding the Server header field.

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2019-05-19 16:13:23 UTC  
> Url: https://github.com/boostorg/beast/pull/1613#issuecomment-493770124  

# [Codecov](https://codecov.io/gh/boostorg/beast/pull/1613?src=pr&el=h1) Report  
> Merging [#1613](https://codecov.io/gh/boostorg/beast/pull/1613?src=pr&el=desc) into [develop](https://codecov.io/gh/boostorg/beast/commit/d1dabebd8352ce0e854474837ed11a27cb688edc?src=pr&el=desc) will **decrease** coverage by `<.01%`.  
> The diff coverage is `100%`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/beast/pull/1613/graphs/tree.svg?width=650&token=Gq6MFA9JRF&height=150&src=pr)](https://codecov.io/gh/boostorg/beast/pull/1613?src=pr&el=tree)  
  
```diff  
@@             Coverage Diff             @@  
##           develop    #1613      +/-   ##  
===========================================  
- Coverage    92.66%   92.66%   -0.01%       
===========================================  
  Files          151      151                
  Lines        12132    12131       -1       
===========================================  
- Hits         11242    11241       -1       
  Misses         890      890  
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/beast/pull/1613?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [include/boost/beast/websocket/impl/accept.hpp](https://codecov.io/gh/boostorg/beast/pull/1613/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC93ZWJzb2NrZXQvaW1wbC9hY2NlcHQuaHBw) | `100% <100%> (ø)` | :arrow_up: |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/beast/pull/1613?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/beast/pull/1613?src=pr&el=footer). Last update [d1dabeb...7f5aa24](https://codecov.io/gh/boostorg/beast/pull/1613?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---
