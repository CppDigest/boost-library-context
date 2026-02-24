# #413 Version 47 [Merged]

> Username: vinniefalco  
> Created at: 2017-06-03 02:14:26 UTC  
> Updated at: 2017-06-03 22:39:11 UTC  
> Merged at: 2017-06-03 22:07:34 UTC  
> Closed at: 2017-06-03 22:07:34 UTC  
> Url: https://github.com/boostorg/beast/pull/413  

**Note** These changes are extensive  
  
* Disable operator<< for buffer_body  
* buffer_size overload for basic_multi_buffer::const_buffers_type  
  
API Changes:  
  
* Refactor treatment of request-method  
* Refactor treatment of status code and obsolete reason  
* Refactor HTTP serialization and parsing  
  
Documentation preview:  
http://vinniefalco.github.io/stage/beast/v47/

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2017-06-03 02:42:20 UTC  
> Updated_at: 2017-06-03 22:39:11 UTC  
> Url: https://github.com/boostorg/beast/pull/413#issuecomment-305945441  

# [Codecov](https://codecov.io/gh/vinniefalco/Beast/pull/413?src=pr&el=h1) Report  
> Merging [#413](https://codecov.io/gh/vinniefalco/Beast/pull/413?src=pr&el=desc) into [master](https://codecov.io/gh/vinniefalco/Beast/commit/3ae76d0d00cf6213f13d3bdf6b4c58754e7e5111?src=pr&el=desc) will **increase** coverage by `0.6%`.  
> The diff coverage is `95.86%`.  
  
[![Impacted file tree graph](https://codecov.io/gh/vinniefalco/Beast/pull/413/graphs/tree.svg?width=650&height=150&src=pr&token=Gq6MFA9JRF)](https://codecov.io/gh/vinniefalco/Beast/pull/413?src=pr&el=tree)  
  
```diff  
@@           Coverage Diff            @@  
##           master    #413     +/-   ##  
========================================  
+ Coverage   93.49%   94.1%   +0.6%       
========================================  
  Files          94      94               
  Lines        6637    6678     +41       
========================================  
+ Hits         6205    6284     +79       
+ Misses        432     394     -38  
```  
  
  
| [Impacted Files](https://codecov.io/gh/vinniefalco/Beast/pull/413?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [include/beast/http/empty\_body.hpp](https://codecov.io/gh/vinniefalco/Beast/pull/413?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9odHRwL2VtcHR5X2JvZHkuaHBw) | `100% <ø> (ø)` | :arrow_up: |  
| [include/beast/http/impl/message.ipp](https://codecov.io/gh/vinniefalco/Beast/pull/413?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9odHRwL2ltcGwvbWVzc2FnZS5pcHA=) | `91.89% <ø> (-0.32%)` | :arrow_down: |  
| [include/beast/http/impl/message\_parser.ipp](https://codecov.io/gh/vinniefalco/Beast/pull/413?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9odHRwL2ltcGwvbWVzc2FnZV9wYXJzZXIuaXBw) | `100% <ø> (ø)` | :arrow_up: |  
| [include/beast/http/serializer.hpp](https://codecov.io/gh/vinniefalco/Beast/pull/413?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9odHRwL3NlcmlhbGl6ZXIuaHBw) | `100% <ø> (ø)` | :arrow_up: |  
| [include/beast/websocket/impl/accept.ipp](https://codecov.io/gh/vinniefalco/Beast/pull/413?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC93ZWJzb2NrZXQvaW1wbC9hY2NlcHQuaXBw) | `100% <ø> (ø)` | :arrow_up: |  
| [include/beast/http/message.hpp](https://codecov.io/gh/vinniefalco/Beast/pull/413?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9odHRwL21lc3NhZ2UuaHBw) | `100% <ø> (ø)` | :arrow_up: |  
| [include/beast/http/fields.hpp](https://codecov.io/gh/vinniefalco/Beast/pull/413?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9odHRwL2ZpZWxkcy5ocHA=) | `100% <ø> (ø)` | :arrow_up: |  
| [include/beast/websocket/impl/read.ipp](https://codecov.io/gh/vinniefalco/Beast/pull/413?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC93ZWJzb2NrZXQvaW1wbC9yZWFkLmlwcA==) | `88.65% <0%> (+0.55%)` | :arrow_up: |  
| [include/beast/http/impl/fields.ipp](https://codecov.io/gh/vinniefalco/Beast/pull/413?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9odHRwL2ltcGwvZmllbGRzLmlwcA==) | `100% <100%> (+2.85%)` | :arrow_up: |  
| [include/beast/http/impl/status.ipp](https://codecov.io/gh/vinniefalco/Beast/pull/413?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9odHRwL2ltcGwvc3RhdHVzLmlwcA==) | `100% <100%> (ø)` | |  
| ... and [31 more](https://codecov.io/gh/vinniefalco/Beast/pull/413?src=pr&el=tree-more) | |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/vinniefalco/Beast/pull/413?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/vinniefalco/Beast/pull/413?src=pr&el=footer). Last update [3ae76d0...458fa7e](https://codecov.io/gh/vinniefalco/Beast/pull/413?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---
