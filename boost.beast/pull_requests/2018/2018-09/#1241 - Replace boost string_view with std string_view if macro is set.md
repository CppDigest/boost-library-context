# #1241 Replace boost string_view with std string_view if macro is set. [Closed]

> Username: do-m-en  
> Created at: 2018-09-05 19:25:40 UTC  
> Updated at: 2018-10-07 19:19:01 UTC  
> Closed at: 2018-10-07 18:28:17 UTC  
> Url: https://github.com/boostorg/beast/pull/1241  

Closes #1133   
  
The solution uses a naive approach of expecting the user knows whether compiler implements std::string_view (not using/defining any compatibility check macros)  and also ignoring the <experimental/string_view> version.

---

## Comment 1

> Username: vinniefalco  
> Created_at: 2018-09-05 19:29:36 UTC  
> Updated_at: 2018-09-05 19:30:37 UTC  
> Url: https://github.com/boostorg/beast/pull/1241#issuecomment-418853110  

LOL is that it?! I did most of the heavy lifting and didn't seem to cross the finish line... Thanks!!  
  
Also, I agree that a simple macro which the user decides whether or not to set makes sense.

---

## Comment 2

> Username: codecov[bot]  
> Created_at: 2018-09-05 21:29:26 UTC  
> Updated_at: 2018-09-24 21:05:09 UTC  
> Url: https://github.com/boostorg/beast/pull/1241#issuecomment-418887764  

# [Codecov](https://codecov.io/gh/boostorg/beast/pull/1241?src=pr&el=h1) Report  
> Merging [#1241](https://codecov.io/gh/boostorg/beast/pull/1241?src=pr&el=desc) into [develop](https://codecov.io/gh/boostorg/beast/commit/1da229a27c6f0539d422bcedbcf47cfe2517164a?src=pr&el=desc) will **decrease** coverage by `0.07%`.  
> The diff coverage is `90.56%`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/beast/pull/1241/graphs/tree.svg?width=650&token=Gq6MFA9JRF&height=150&src=pr)](https://codecov.io/gh/boostorg/beast/pull/1241?src=pr&el=tree)  
  
```diff  
@@             Coverage Diff             @@  
##           develop    #1241      +/-   ##  
===========================================  
- Coverage    95.52%   95.44%   -0.08%       
===========================================  
  Files          113      112       -1       
  Lines        11064    11074      +10       
===========================================  
+ Hits         10569    10570       +1       
- Misses         495      504       +9  
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/beast/pull/1241?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [include/boost/beast/core/impl/read\_size.ipp](https://codecov.io/gh/boostorg/beast/pull/1241/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9jb3JlL2ltcGwvcmVhZF9zaXplLmlwcA==) | `100% <ø> (ø)` | :arrow_up: |  
| [include/boost/beast/websocket/impl/write.ipp](https://codecov.io/gh/boostorg/beast/pull/1241/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC93ZWJzb2NrZXQvaW1wbC93cml0ZS5pcHA=) | `97.98% <ø> (ø)` | :arrow_up: |  
| [include/boost/beast/http/impl/message.ipp](https://codecov.io/gh/boostorg/beast/pull/1241/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9odHRwL2ltcGwvbWVzc2FnZS5pcHA=) | `96.03% <ø> (ø)` | :arrow_up: |  
| [include/boost/beast/core/string.hpp](https://codecov.io/gh/boostorg/beast/pull/1241/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9jb3JlL3N0cmluZy5ocHA=) | `100% <ø> (ø)` | :arrow_up: |  
| [include/boost/beast/core/impl/flat\_buffer.ipp](https://codecov.io/gh/boostorg/beast/pull/1241/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9jb3JlL2ltcGwvZmxhdF9idWZmZXIuaXBw) | `99.5% <100%> (ø)` | :arrow_up: |  
| [include/boost/beast/http/detail/rfc7230.hpp](https://codecov.io/gh/boostorg/beast/pull/1241/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9odHRwL2RldGFpbC9yZmM3MjMwLmhwcA==) | `85.96% <100%> (ø)` | :arrow_up: |  
| [include/boost/beast/http/impl/verb.ipp](https://codecov.io/gh/boostorg/beast/pull/1241/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9odHRwL2ltcGwvdmVyYi5pcHA=) | `98.79% <100%> (ø)` | :arrow_up: |  
| [include/boost/beast/http/impl/field.ipp](https://codecov.io/gh/boostorg/beast/pull/1241/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9odHRwL2ltcGwvZmllbGQuaXBw) | `100% <100%> (ø)` | :arrow_up: |  
| [include/boost/beast/core/multi\_buffer.hpp](https://codecov.io/gh/boostorg/beast/pull/1241/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9jb3JlL211bHRpX2J1ZmZlci5ocHA=) | `100% <100%> (ø)` | :arrow_up: |  
| [include/boost/beast/core/flat\_buffer.hpp](https://codecov.io/gh/boostorg/beast/pull/1241/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9jb3JlL2ZsYXRfYnVmZmVyLmhwcA==) | `100% <100%> (ø)` | :arrow_up: |  
| ... and [6 more](https://codecov.io/gh/boostorg/beast/pull/1241/diff?src=pr&el=tree-more) | |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/beast/pull/1241?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/beast/pull/1241?src=pr&el=footer). Last update [1da229a...c214aff](https://codecov.io/gh/boostorg/beast/pull/1241?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---
