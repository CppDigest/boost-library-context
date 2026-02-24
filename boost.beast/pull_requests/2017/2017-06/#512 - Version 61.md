# #512 Version 61 [Merged]

> Username: vinniefalco  
> Created at: 2017-06-18 23:57:47 UTC  
> Updated at: 2017-06-20 01:55:32 UTC  
> Merged at: 2017-06-19 17:51:29 UTC  
> Closed at: 2017-06-19 17:51:29 UTC  
> Url: https://github.com/boostorg/beast/pull/512  

* Remove Spirit dependency  
* Use generic_cateogry for errno  
* Reorganize SSL examples  
* Tidy up some integer conversion warnings  
* Add message::header_part()  
* Tidy up names in error categories  
* Flush the output stream in the example  
* Clean close in Secure WebSocket client  
* Add server-framework SSL HTTP and WebSocket ports  
* Fix shadowing warnings  
* Tidy up http-crawl example  
* Add multi_port to server-framework  
  
API Changes:  
  
* header::version is unsigned  
* status-codes is unsigned  
  
Documentation preview:  
http://vinniefalco.github.io/stage/beast/v61/

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2017-06-19 01:22:03 UTC  
> Updated_at: 2017-06-19 19:15:11 UTC  
> Url: https://github.com/boostorg/beast/pull/512#issuecomment-309317217  

# [Codecov](https://codecov.io/gh/vinniefalco/Beast/pull/512?src=pr&el=h1) Report  
> Merging [#512](https://codecov.io/gh/vinniefalco/Beast/pull/512?src=pr&el=desc) into [master](https://codecov.io/gh/vinniefalco/Beast/commit/c111d6ff03c9d1fd21226ca6f2f647ff3dc7aa1d?src=pr&el=desc) will **decrease** coverage by `10.04%`.  
> The diff coverage is `64.7%`.  
  
[![Impacted file tree graph](https://codecov.io/gh/vinniefalco/Beast/pull/512/graphs/tree.svg?token=Gq6MFA9JRF&width=650&height=150&src=pr)](https://codecov.io/gh/vinniefalco/Beast/pull/512?src=pr&el=tree)  
  
```diff  
@@             Coverage Diff             @@  
##           master     #512       +/-   ##  
===========================================  
- Coverage   93.46%   83.41%   -10.05%       
===========================================  
  Files          96       96                 
  Lines        7095     7098        +3       
===========================================  
- Hits         6631     5921      -710       
- Misses        464     1177      +713  
```  
  
  
| [Impacted Files](https://codecov.io/gh/vinniefalco/Beast/pull/512?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [include/beast/http/message.hpp](https://codecov.io/gh/vinniefalco/Beast/pull/512?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9odHRwL21lc3NhZ2UuaHBw) | `100% <ø> (ø)` | :arrow_up: |  
| [include/beast/core/error.hpp](https://codecov.io/gh/vinniefalco/Beast/pull/512?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9jb3JlL2Vycm9yLmhwcA==) | `100% <ø> (ø)` | :arrow_up: |  
| [include/beast/http/impl/fields.ipp](https://codecov.io/gh/vinniefalco/Beast/pull/512?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9odHRwL2ltcGwvZmllbGRzLmlwcA==) | `90.79% <ø> (ø)` | :arrow_up: |  
| [include/beast/websocket/stream.hpp](https://codecov.io/gh/vinniefalco/Beast/pull/512?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC93ZWJzb2NrZXQvc3RyZWFtLmhwcA==) | `90.47% <ø> (ø)` | :arrow_up: |  
| [include/beast/core/string.hpp](https://codecov.io/gh/vinniefalco/Beast/pull/512?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9jb3JlL3N0cmluZy5ocHA=) | `100% <100%> (ø)` | :arrow_up: |  
| [include/beast/zlib/impl/error.ipp](https://codecov.io/gh/vinniefalco/Beast/pull/512?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC96bGliL2ltcGwvZXJyb3IuaXBw) | `100% <100%> (ø)` | :arrow_up: |  
| [include/beast/http/impl/message.ipp](https://codecov.io/gh/vinniefalco/Beast/pull/512?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9odHRwL2ltcGwvbWVzc2FnZS5pcHA=) | `89.33% <100%> (-0.15%)` | :arrow_down: |  
| [include/beast/http/impl/parser.ipp](https://codecov.io/gh/vinniefalco/Beast/pull/512?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9odHRwL2ltcGwvcGFyc2VyLmlwcA==) | `70% <100%> (ø)` | :arrow_up: |  
| [include/beast/websocket/impl/stream.ipp](https://codecov.io/gh/vinniefalco/Beast/pull/512?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC93ZWJzb2NrZXQvaW1wbC9zdHJlYW0uaXBw) | `91.03% <100%> (-4.14%)` | :arrow_down: |  
| [include/beast/http/impl/status.ipp](https://codecov.io/gh/vinniefalco/Beast/pull/512?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9odHRwL2ltcGwvc3RhdHVzLmlwcA==) | `100% <100%> (ø)` | :arrow_up: |  
| ... and [22 more](https://codecov.io/gh/vinniefalco/Beast/pull/512?src=pr&el=tree-more) | |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/vinniefalco/Beast/pull/512?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/vinniefalco/Beast/pull/512?src=pr&el=footer). Last update [c111d6f...4c15db4](https://codecov.io/gh/vinniefalco/Beast/pull/512?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---

## Comment 2

> Username: vinniefalco  
> Created_at: 2017-06-19 01:41:41 UTC  
> Url: https://github.com/boostorg/beast/pull/512#issuecomment-309319082  

Can someone please audit the changes to the implementation, e,g, everything in `include/beast`?

---
