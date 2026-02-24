# #507 Version 61 [Closed]

> Username: vinniefalco  
> Created at: 2017-06-18 04:51:49 UTC  
> Updated at: 2017-06-18 21:32:56 UTC  
> Closed at: 2017-06-18 21:32:12 UTC  
> Url: https://github.com/boostorg/beast/pull/507  

* Remove Spirit dependency  
* Use generic_cateogry for errno  
* Reorganize SSL examples  
* Tidy up some integer conversion warnings  
* Add message::header_part()  
* Tidy up names in error categories  
* Flush the output stream in the example  
* Clean close in Secure WebSocket client  
* Add server-framework SSL HTTP and WebSocket ports  
  
API Changes:  
  
* header::version is unsigned  
* status-codes is unsigned  
  
Documentation preview:  
http://vinniefalco.github.io/stage/beast/v61

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2017-06-18 07:27:34 UTC  
> Updated_at: 2017-06-18 21:32:56 UTC  
> Url: https://github.com/boostorg/beast/pull/507#issuecomment-309261723  

# [Codecov](https://codecov.io/gh/vinniefalco/Beast/pull/507?src=pr&el=h1) Report  
> Merging [#507](https://codecov.io/gh/vinniefalco/Beast/pull/507?src=pr&el=desc) into [master](https://codecov.io/gh/vinniefalco/Beast/commit/c111d6ff03c9d1fd21226ca6f2f647ff3dc7aa1d?src=pr&el=desc) will **decrease** coverage by `0.01%`.  
> The diff coverage is `85.71%`.  
  
[![Impacted file tree graph](https://codecov.io/gh/vinniefalco/Beast/pull/507/graphs/tree.svg?src=pr&token=Gq6MFA9JRF&width=650&height=150)](https://codecov.io/gh/vinniefalco/Beast/pull/507?src=pr&el=tree)  
  
```diff  
@@            Coverage Diff             @@  
##           master     #507      +/-   ##  
==========================================  
- Coverage   93.46%   93.44%   -0.02%       
==========================================  
  Files          96       96                
  Lines        7095     7098       +3       
==========================================  
+ Hits         6631     6633       +2       
- Misses        464      465       +1  
```  
  
  
| [Impacted Files](https://codecov.io/gh/vinniefalco/Beast/pull/507?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [include/beast/http/impl/fields.ipp](https://codecov.io/gh/vinniefalco/Beast/pull/507?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9odHRwL2ltcGwvZmllbGRzLmlwcA==) | `90.79% <ø> (ø)` | :arrow_up: |  
| [include/beast/http/message.hpp](https://codecov.io/gh/vinniefalco/Beast/pull/507?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9odHRwL21lc3NhZ2UuaHBw) | `100% <ø> (ø)` | :arrow_up: |  
| [include/beast/core/error.hpp](https://codecov.io/gh/vinniefalco/Beast/pull/507?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9jb3JlL2Vycm9yLmhwcA==) | `100% <ø> (ø)` | :arrow_up: |  
| [include/beast/http/impl/status.ipp](https://codecov.io/gh/vinniefalco/Beast/pull/507?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9odHRwL2ltcGwvc3RhdHVzLmlwcA==) | `100% <100%> (ø)` | :arrow_up: |  
| [include/beast/http/impl/message.ipp](https://codecov.io/gh/vinniefalco/Beast/pull/507?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9odHRwL2ltcGwvbWVzc2FnZS5pcHA=) | `89.33% <100%> (-0.15%)` | :arrow_down: |  
| [include/beast/zlib/impl/error.ipp](https://codecov.io/gh/vinniefalco/Beast/pull/507?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC96bGliL2ltcGwvZXJyb3IuaXBw) | `100% <100%> (ø)` | :arrow_up: |  
| [include/beast/core/string.hpp](https://codecov.io/gh/vinniefalco/Beast/pull/507?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9jb3JlL3N0cmluZy5ocHA=) | `100% <100%> (ø)` | :arrow_up: |  
| [include/beast/http/impl/error.ipp](https://codecov.io/gh/vinniefalco/Beast/pull/507?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9odHRwL2ltcGwvZXJyb3IuaXBw) | `97.22% <50%> (+0.07%)` | :arrow_up: |  
| [include/beast/websocket/impl/error.ipp](https://codecov.io/gh/vinniefalco/Beast/pull/507?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC93ZWJzb2NrZXQvaW1wbC9lcnJvci5pcHA=) | `95.23% <50%> (ø)` | :arrow_up: |  
| [include/beast/core/drain\_buffer.hpp](https://codecov.io/gh/vinniefalco/Beast/pull/507?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9jb3JlL2RyYWluX2J1ZmZlci5ocHA=) | `88.88% <0%> (-11.12%)` | :arrow_down: |  
| ... and [1 more](https://codecov.io/gh/vinniefalco/Beast/pull/507?src=pr&el=tree-more) | |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/vinniefalco/Beast/pull/507?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/vinniefalco/Beast/pull/507?src=pr&el=footer). Last update [c111d6f...1729ce0](https://codecov.io/gh/vinniefalco/Beast/pull/507?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---
