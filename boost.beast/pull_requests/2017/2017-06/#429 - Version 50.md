# #429 Version 50 [Merged]

> Username: vinniefalco  
> Created at: 2017-06-07 12:22:10 UTC  
> Updated at: 2017-06-08 03:39:22 UTC  
> Merged at: 2017-06-08 03:14:42 UTC  
> Closed at: 2017-06-08 03:14:42 UTC  
> Url: https://github.com/boostorg/beast/pull/429  

* parser is constructible from other body types  
* Add field enumeration  
* Use allocator more in basic_fields  
* Fix basic_fields allocator awareness  
* Use field in basic_fields and call sites  
* Use field in basic_parser  
* Tidy up basic_fields, header, and field concepts  
* Fields concept work  
* Body documentation work  
  
API Changes:  
  
* Remove header_parser  
* Add verb to on_request for parsers  
* Refactor prepare  
* Protect basic_fields special members  
* Remove message connection settings  
* Remove message free functions  
* Remove obsolete serializer allocator  
  
Documentation preview:  
http://vinniefalco.github.io/stage/beast/v50

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2017-06-07 12:56:21 UTC  
> Updated_at: 2017-06-08 03:39:22 UTC  
> Url: https://github.com/boostorg/beast/pull/429#issuecomment-306786200  

# [Codecov](https://codecov.io/gh/vinniefalco/Beast/pull/429?src=pr&el=h1) Report  
> Merging [#429](https://codecov.io/gh/vinniefalco/Beast/pull/429?src=pr&el=desc) into [master](https://codecov.io/gh/vinniefalco/Beast/commit/ac5bc4f62c318812104ad2cef13b2d3a19ada8bb?src=pr&el=desc) will **decrease** coverage by `0.21%`.  
> The diff coverage is `95.38%`.  
  
[![Impacted file tree graph](https://codecov.io/gh/vinniefalco/Beast/pull/429/graphs/tree.svg?width=650&height=150&src=pr&token=Gq6MFA9JRF)](https://codecov.io/gh/vinniefalco/Beast/pull/429?src=pr&el=tree)  
  
```diff  
@@            Coverage Diff             @@  
##           master     #429      +/-   ##  
==========================================  
- Coverage      94%   93.79%   -0.22%       
==========================================  
  Files          93       92       -1       
  Lines        6676     6723      +47       
==========================================  
+ Hits         6276     6306      +30       
- Misses        400      417      +17  
```  
  
  
| [Impacted Files](https://codecov.io/gh/vinniefalco/Beast/pull/429?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [include/beast/http/basic\_parser.hpp](https://codecov.io/gh/vinniefalco/Beast/pull/429?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9odHRwL2Jhc2ljX3BhcnNlci5ocHA=) | `100% <ø> (ø)` | :arrow_up: |  
| [include/beast/core/buffer\_cat.hpp](https://codecov.io/gh/vinniefalco/Beast/pull/429?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9jb3JlL2J1ZmZlcl9jYXQuaHBw) | `100% <ø> (ø)` | :arrow_up: |  
| [include/beast/http/empty\_body.hpp](https://codecov.io/gh/vinniefalco/Beast/pull/429?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9odHRwL2VtcHR5X2JvZHkuaHBw) | `66.66% <ø> (ø)` | :arrow_up: |  
| [include/beast/http/dynamic\_body.hpp](https://codecov.io/gh/vinniefalco/Beast/pull/429?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9odHRwL2R5bmFtaWNfYm9keS5ocHA=) | `77.77% <ø> (ø)` | :arrow_up: |  
| [include/beast/core/impl/buffer\_cat.ipp](https://codecov.io/gh/vinniefalco/Beast/pull/429?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9jb3JlL2ltcGwvYnVmZmVyX2NhdC5pcHA=) | `97.52% <ø> (ø)` | :arrow_up: |  
| [include/beast/websocket/stream.hpp](https://codecov.io/gh/vinniefalco/Beast/pull/429?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC93ZWJzb2NrZXQvc3RyZWFtLmhwcA==) | `100% <ø> (ø)` | :arrow_up: |  
| [include/beast/http/buffer\_body.hpp](https://codecov.io/gh/vinniefalco/Beast/pull/429?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9odHRwL2J1ZmZlcl9ib2R5LmhwcA==) | `100% <ø> (ø)` | :arrow_up: |  
| [include/beast/core/handler\_alloc.hpp](https://codecov.io/gh/vinniefalco/Beast/pull/429?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9jb3JlL2hhbmRsZXJfYWxsb2MuaHBw) | `100% <ø> (ø)` | :arrow_up: |  
| [include/beast/websocket/impl/handshake.ipp](https://codecov.io/gh/vinniefalco/Beast/pull/429?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC93ZWJzb2NrZXQvaW1wbC9oYW5kc2hha2UuaXBw) | `96.59% <100%> (ø)` | :arrow_up: |  
| [include/beast/http/impl/write.ipp](https://codecov.io/gh/vinniefalco/Beast/pull/429?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9odHRwL2ltcGwvd3JpdGUuaXBw) | `93.25% <100%> (-0.19%)` | :arrow_down: |  
| ... and [31 more](https://codecov.io/gh/vinniefalco/Beast/pull/429?src=pr&el=tree-more) | |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/vinniefalco/Beast/pull/429?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/vinniefalco/Beast/pull/429?src=pr&el=footer). Last update [ac5bc4f...a2c1117](https://codecov.io/gh/vinniefalco/Beast/pull/429?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---
