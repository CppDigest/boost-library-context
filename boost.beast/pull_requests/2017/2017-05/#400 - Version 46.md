# #400 Version 46 [Merged]

> Username: vinniefalco  
> Created at: 2017-05-30 09:18:57 UTC  
> Updated at: 2017-05-31 02:18:56 UTC  
> Merged at: 2017-05-31 00:44:08 UTC  
> Closed at: 2017-05-31 00:44:08 UTC  
> Url: https://github.com/boostorg/beast/pull/400  

* Add test::pipe  
* Documentation work  
  
API Changes:  
  
* Remove HTTP header aliases  
* Refactor HTTP serialization  
* Refactor type traits  
  
Documentation preview:  
http://vinniefalco.github.io/stage/beast/v46/

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2017-05-30 09:59:35 UTC  
> Updated_at: 2017-05-31 01:20:46 UTC  
> Url: https://github.com/boostorg/beast/pull/400#issuecomment-304831868  

# [Codecov](https://codecov.io/gh/vinniefalco/Beast/pull/400?src=pr&el=h1) Report  
> Merging [#400](https://codecov.io/gh/vinniefalco/Beast/pull/400?src=pr&el=desc) into [master](https://codecov.io/gh/vinniefalco/Beast/commit/dfba72b6aaf4afb255fe2f516b753456a6f41d67?src=pr&el=desc) will **decrease** coverage by `<.01%`.  
> The diff coverage is `99.63%`.  
  
[![Impacted file tree graph](https://codecov.io/gh/vinniefalco/Beast/pull/400/graphs/tree.svg?src=pr&token=Gq6MFA9JRF&width=650&height=150)](https://codecov.io/gh/vinniefalco/Beast/pull/400?src=pr&el=tree)  
  
```diff  
@@            Coverage Diff             @@  
##           master     #400      +/-   ##  
==========================================  
- Coverage   93.49%   93.49%   -0.01%       
==========================================  
  Files          92       94       +2       
  Lines        6553     6637      +84       
==========================================  
+ Hits         6127     6205      +78       
- Misses        426      432       +6  
```  
  
  
| [Impacted Files](https://codecov.io/gh/vinniefalco/Beast/pull/400?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [include/beast/websocket/impl/stream.ipp](https://codecov.io/gh/vinniefalco/Beast/pull/400?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC93ZWJzb2NrZXQvaW1wbC9zdHJlYW0uaXBw) | `95.33% <ø> (ø)` | :arrow_up: |  
| [include/beast/http/impl/async\_read.ipp](https://codecov.io/gh/vinniefalco/Beast/pull/400?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9odHRwL2ltcGwvYXN5bmNfcmVhZC5pcHA=) | `72% <ø> (+0.57%)` | :arrow_up: |  
| [include/beast/http/impl/read.ipp](https://codecov.io/gh/vinniefalco/Beast/pull/400?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9odHRwL2ltcGwvcmVhZC5pcHA=) | `87.5% <ø> (ø)` | :arrow_up: |  
| [include/beast/websocket/impl/accept.ipp](https://codecov.io/gh/vinniefalco/Beast/pull/400?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC93ZWJzb2NrZXQvaW1wbC9hY2NlcHQuaXBw) | `100% <ø> (ø)` | :arrow_up: |  
| [include/beast/websocket/impl/handshake.ipp](https://codecov.io/gh/vinniefalco/Beast/pull/400?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC93ZWJzb2NrZXQvaW1wbC9oYW5kc2hha2UuaXBw) | `100% <ø> (ø)` | :arrow_up: |  
| [include/beast/http/fields.hpp](https://codecov.io/gh/vinniefalco/Beast/pull/400?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9odHRwL2ZpZWxkcy5ocHA=) | `100% <ø> (ø)` | :arrow_up: |  
| [include/beast/http/message.hpp](https://codecov.io/gh/vinniefalco/Beast/pull/400?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9odHRwL21lc3NhZ2UuaHBw) | `100% <ø> (ø)` | :arrow_up: |  
| [include/beast/websocket/stream.hpp](https://codecov.io/gh/vinniefalco/Beast/pull/400?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC93ZWJzb2NrZXQvc3RyZWFtLmhwcA==) | `100% <ø> (ø)` | :arrow_up: |  
| [include/beast/http/rfc7230.hpp](https://codecov.io/gh/vinniefalco/Beast/pull/400?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9odHRwL3JmYzcyMzAuaHBw) | `100% <ø> (ø)` | :arrow_up: |  
| [include/beast/http/buffer\_body.hpp](https://codecov.io/gh/vinniefalco/Beast/pull/400?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9odHRwL2J1ZmZlcl9ib2R5LmhwcA==) | `100% <100%> (ø)` | |  
| ... and [23 more](https://codecov.io/gh/vinniefalco/Beast/pull/400?src=pr&el=tree-more) | |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/vinniefalco/Beast/pull/400?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/vinniefalco/Beast/pull/400?src=pr&el=footer). Last update [dfba72b...3ae76d0](https://codecov.io/gh/vinniefalco/Beast/pull/400?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---
