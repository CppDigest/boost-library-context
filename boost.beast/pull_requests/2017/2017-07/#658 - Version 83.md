# #658 Version 83 [Merged]

> Username: vinniefalco  
> Created at: 2017-07-16 14:12:34 UTC  
> Updated at: 2017-07-20 14:46:32 UTC  
> Merged at: 2017-07-18 23:57:58 UTC  
> Closed at: 2017-07-18 23:57:58 UTC  
> Url: https://github.com/boostorg/beast/pull/658  

* Fix extraneous argument in async_write_header  
* Add flat_static_buffer::mutable_data  
* Add buffer_front  
* Add wstest compression option  
* Turn some flat_static_buffer_tests on  
  
WebSocket  
  
* Add wstest compression option  
* Fix buffer lifetime in websocket write  
  
API Changes:  
  
* Add static_buffer   
  
Actions Required:  
  
* Callers who depend on static_buffer returning sequences of  
  exactly length one should switch to flat_static_buffer.  
  
Documentation preview:  
http://vinniefalco.github.io/stage/beast/v83

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2017-07-16 15:03:28 UTC  
> Updated_at: 2017-07-19 01:42:38 UTC  
> Url: https://github.com/boostorg/beast/pull/658#issuecomment-315615136  

# [Codecov](https://codecov.io/gh/vinniefalco/Beast/pull/658?src=pr&el=h1) Report  
> Merging [#658](https://codecov.io/gh/vinniefalco/Beast/pull/658?src=pr&el=desc) into [develop](https://codecov.io/gh/vinniefalco/Beast/commit/37199d4d4ae5eff11d59c60473871e5b6b7fa3e8?src=pr&el=desc) will **increase** coverage by `0.04%`.  
> The diff coverage is `100%`.  
  
[![Impacted file tree graph](https://codecov.io/gh/vinniefalco/Beast/pull/658/graphs/tree.svg?height=150&width=650&token=Gq6MFA9JRF&src=pr)](https://codecov.io/gh/vinniefalco/Beast/pull/658?src=pr&el=tree)  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #658      +/-   ##  
===========================================  
+ Coverage    93.99%   94.03%   +0.04%       
===========================================  
  Files          106      108       +2       
  Lines         8073     8136      +63       
===========================================  
+ Hits          7588     7651      +63       
  Misses         485      485  
```  
  
  
| [Impacted Files](https://codecov.io/gh/vinniefalco/Beast/pull/658?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [include/beast/core/flat\_static\_buffer.hpp](https://codecov.io/gh/vinniefalco/Beast/pull/658?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9jb3JlL2ZsYXRfc3RhdGljX2J1ZmZlci5ocHA=) | `100% <ø> (ø)` | :arrow_up: |  
| [include/beast/http/impl/chunk\_encode.ipp](https://codecov.io/gh/vinniefalco/Beast/pull/658?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9odHRwL2ltcGwvY2h1bmtfZW5jb2RlLmlwcA==) | `100% <ø> (ø)` | :arrow_up: |  
| [include/beast/http/basic\_parser.hpp](https://codecov.io/gh/vinniefalco/Beast/pull/658?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9odHRwL2Jhc2ljX3BhcnNlci5ocHA=) | `100% <ø> (ø)` | :arrow_up: |  
| [include/beast/http/fields.hpp](https://codecov.io/gh/vinniefalco/Beast/pull/658?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9odHRwL2ZpZWxkcy5ocHA=) | `100% <ø> (ø)` | :arrow_up: |  
| [include/beast/core/buffer\_prefix.hpp](https://codecov.io/gh/vinniefalco/Beast/pull/658?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9jb3JlL2J1ZmZlcl9wcmVmaXguaHBw) | `100% <ø> (ø)` | :arrow_up: |  
| [include/beast/core/impl/static\_buffer.ipp](https://codecov.io/gh/vinniefalco/Beast/pull/658?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9jb3JlL2ltcGwvc3RhdGljX2J1ZmZlci5pcHA=) | `100% <100%> (ø)` | |  
| [include/beast/core/impl/flat\_static\_buffer.ipp](https://codecov.io/gh/vinniefalco/Beast/pull/658?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9jb3JlL2ltcGwvZmxhdF9zdGF0aWNfYnVmZmVyLmlwcA==) | `100% <100%> (ø)` | :arrow_up: |  
| [include/beast/websocket/impl/write.ipp](https://codecov.io/gh/vinniefalco/Beast/pull/658?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC93ZWJzb2NrZXQvaW1wbC93cml0ZS5pcHA=) | `87.53% <100%> (+0.03%)` | :arrow_up: |  
| [include/beast/http/string\_body.hpp](https://codecov.io/gh/vinniefalco/Beast/pull/658?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9odHRwL3N0cmluZ19ib2R5LmhwcA==) | `86.2% <100%> (+1.59%)` | :arrow_up: |  
| [include/beast/core/static\_buffer.hpp](https://codecov.io/gh/vinniefalco/Beast/pull/658?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9jb3JlL3N0YXRpY19idWZmZXIuaHBw) | `100% <100%> (ø)` | |  
| ... and [3 more](https://codecov.io/gh/vinniefalco/Beast/pull/658?src=pr&el=tree-more) | |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/vinniefalco/Beast/pull/658?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/vinniefalco/Beast/pull/658?src=pr&el=footer). Last update [37199d4...093f446](https://codecov.io/gh/vinniefalco/Beast/pull/658?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---
