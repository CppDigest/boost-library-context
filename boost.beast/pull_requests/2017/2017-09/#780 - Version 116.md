# #780 Version 116 [Merged]

> Username: vinniefalco  
> Created at: 2017-09-13 00:17:12 UTC  
> Updated at: 2017-10-01 17:09:07 UTC  
> Merged at: 2017-09-13 01:19:58 UTC  
> Closed at: 2017-09-13 01:19:58 UTC  
> Url: https://github.com/boostorg/beast/pull/780  

API Changes:  
  
* message::body is a member function  
* message::version is a member function  
  
Actions Required:  
  
* Call member function message::body instead of accessing  
  the data member at call sites.  
  
* Call member function message::version instead of accessing  
  the version member at call sites.

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2017-09-13 00:26:58 UTC  
> Url: https://github.com/boostorg/beast/pull/780#issuecomment-329022178  

# [Codecov](https://codecov.io/gh/boostorg/beast/pull/780?src=pr&el=h1) Report  
> Merging [#780](https://codecov.io/gh/boostorg/beast/pull/780?src=pr&el=desc) into [develop](https://codecov.io/gh/boostorg/beast/commit/0d4d239d05bdf0a12d91432519356634d9e46072?src=pr&el=desc) will **increase** coverage by `<.01%`.  
> The diff coverage is `100%`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/beast/pull/780/graphs/tree.svg?token=Gq6MFA9JRF&src=pr&width=650&height=150)](https://codecov.io/gh/boostorg/beast/pull/780?src=pr&el=tree)  
  
```diff  
@@            Coverage Diff             @@  
##           develop    #780      +/-   ##  
==========================================  
+ Coverage     95.7%   95.7%   +<.01%       
==========================================  
  Files          104     104                
  Lines        10513   10531      +18       
==========================================  
+ Hits         10061   10079      +18       
  Misses         452     452  
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/beast/pull/780?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [include/boost/beast/http/buffer\_body.hpp](https://codecov.io/gh/boostorg/beast/pull/780?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9odHRwL2J1ZmZlcl9ib2R5LmhwcA==) | `100% <ø> (ø)` | :arrow_up: |  
| [include/boost/beast/websocket/stream.hpp](https://codecov.io/gh/boostorg/beast/pull/780?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC93ZWJzb2NrZXQvc3RyZWFtLmhwcA==) | `100% <ø> (ø)` | :arrow_up: |  
| [include/boost/beast/http/impl/message.ipp](https://codecov.io/gh/boostorg/beast/pull/780?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9odHRwL2ltcGwvbWVzc2FnZS5pcHA=) | `95.65% <100%> (ø)` | :arrow_up: |  
| [include/boost/beast/http/parser.hpp](https://codecov.io/gh/boostorg/beast/pull/780?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9odHRwL3BhcnNlci5ocHA=) | `83.01% <100%> (ø)` | :arrow_up: |  
| [include/boost/beast/http/message.hpp](https://codecov.io/gh/boostorg/beast/pull/780?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9odHRwL21lc3NhZ2UuaHBw) | `100% <100%> (ø)` | :arrow_up: |  
| [include/boost/beast/http/impl/serializer.ipp](https://codecov.io/gh/boostorg/beast/pull/780?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9odHRwL2ltcGwvc2VyaWFsaXplci5pcHA=) | `94.63% <100%> (ø)` | :arrow_up: |  
| [include/boost/beast/http/string\_body.hpp](https://codecov.io/gh/boostorg/beast/pull/780?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9odHRwL3N0cmluZ19ib2R5LmhwcA==) | `81.39% <100%> (ø)` | :arrow_up: |  
| [include/boost/beast/http/span\_body.hpp](https://codecov.io/gh/boostorg/beast/pull/780?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9odHRwL3NwYW5fYm9keS5ocHA=) | `93.93% <100%> (ø)` | :arrow_up: |  
| [include/boost/beast/websocket/impl/stream.ipp](https://codecov.io/gh/boostorg/beast/pull/780?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC93ZWJzb2NrZXQvaW1wbC9zdHJlYW0uaXBw) | `100% <100%> (ø)` | :arrow_up: |  
| [include/boost/beast/http/basic\_file\_body.hpp](https://codecov.io/gh/boostorg/beast/pull/780?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9odHRwL2Jhc2ljX2ZpbGVfYm9keS5ocHA=) | `81.35% <100%> (ø)` | :arrow_up: |  
| ... and [4 more](https://codecov.io/gh/boostorg/beast/pull/780?src=pr&el=tree-more) | |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/beast/pull/780?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/beast/pull/780?src=pr&el=footer). Last update [0d4d239...dca6593](https://codecov.io/gh/boostorg/beast/pull/780?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---
