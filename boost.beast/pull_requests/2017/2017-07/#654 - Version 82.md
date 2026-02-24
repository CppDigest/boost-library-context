# #654 Version 82 [Closed]

> Username: vinniefalco  
> Created at: 2017-07-15 04:47:15 UTC  
> Updated at: 2017-07-16 15:17:31 UTC  
> Closed at: 2017-07-16 14:27:04 UTC  
> Url: https://github.com/boostorg/beast/pull/654  

* Documentation tidying  
* is_invocable works with move-only types  
* Use std::function and reference wrapper  
* Add session_alloc to example/common  
  
WebSocket  
  
* stream tidying  
* Add rd_close_ to websocket stream state  
* stream uses flat_buffer  
* accept requires a message  
* Add wstest benchmark tool  
  
API Changes:  
  
* Rename to flat_static_buffer and flat_static_buffer_base  
  
Actions Required:  
  
* Rename static_buffer to flat_static_buffer_base  
* Rename static_buffer_n to flat_static_buffer  
  
Documentation preview:  
http://vinniefalco.github.io/stage/beast/v82

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2017-07-15 10:34:02 UTC  
> Updated_at: 2017-07-15 18:27:59 UTC  
> Url: https://github.com/boostorg/beast/pull/654#issuecomment-315525520  

# [Codecov](https://codecov.io/gh/vinniefalco/Beast/pull/654?src=pr&el=h1) Report  
> Merging [#654](https://codecov.io/gh/vinniefalco/Beast/pull/654?src=pr&el=desc) into [develop](https://codecov.io/gh/vinniefalco/Beast/commit/fde46ebd9f17f8a96a73367b983fe156facc0e18?src=pr&el=desc) will **increase** coverage by `0.09%`.  
> The diff coverage is `97.56%`.  
  
[![Impacted file tree graph](https://codecov.io/gh/vinniefalco/Beast/pull/654/graphs/tree.svg?width=650&height=150&src=pr&token=Gq6MFA9JRF)](https://codecov.io/gh/vinniefalco/Beast/pull/654?src=pr&el=tree)  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #654      +/-   ##  
===========================================  
+ Coverage    93.88%   93.98%   +0.09%       
===========================================  
  Files          106      106                
  Lines         8099     8066      -33       
===========================================  
- Hits          7604     7581      -23       
+ Misses         495      485      -10  
```  
  
  
| [Impacted Files](https://codecov.io/gh/vinniefalco/Beast/pull/654?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [include/beast/http/basic\_parser.hpp](https://codecov.io/gh/vinniefalco/Beast/pull/654?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9odHRwL2Jhc2ljX3BhcnNlci5ocHA=) | `100% <ø> (ø)` | :arrow_up: |  
| [include/beast/http/impl/parser.ipp](https://codecov.io/gh/vinniefalco/Beast/pull/654?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9odHRwL2ltcGwvcGFyc2VyLmlwcA==) | `91.66% <ø> (+4.16%)` | :arrow_up: |  
| [include/beast/core/string\_param.hpp](https://codecov.io/gh/vinniefalco/Beast/pull/654?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9jb3JlL3N0cmluZ19wYXJhbS5ocHA=) | `100% <ø> (ø)` | :arrow_up: |  
| [include/beast/websocket/detail/frame.hpp](https://codecov.io/gh/vinniefalco/Beast/pull/654?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC93ZWJzb2NrZXQvZGV0YWlsL2ZyYW1lLmhwcA==) | `95.34% <ø> (ø)` | :arrow_up: |  
| [include/beast/core/flat\_static\_buffer.hpp](https://codecov.io/gh/vinniefalco/Beast/pull/654?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9jb3JlL2ZsYXRfc3RhdGljX2J1ZmZlci5ocHA=) | `100% <100%> (ø)` | |  
| [include/beast/websocket/impl/ping.ipp](https://codecov.io/gh/vinniefalco/Beast/pull/654?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC93ZWJzb2NrZXQvaW1wbC9waW5nLmlwcA==) | `96.29% <100%> (ø)` | :arrow_up: |  
| [include/beast/websocket/stream.hpp](https://codecov.io/gh/vinniefalco/Beast/pull/654?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC93ZWJzb2NrZXQvc3RyZWFtLmhwcA==) | `90.9% <100%> (-0.76%)` | :arrow_down: |  
| [include/beast/core/impl/flat\_static\_buffer.ipp](https://codecov.io/gh/vinniefalco/Beast/pull/654?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9jb3JlL2ltcGwvZmxhdF9zdGF0aWNfYnVmZmVyLmlwcA==) | `100% <100%> (ø)` | |  
| [include/beast/http/detail/basic\_parser.hpp](https://codecov.io/gh/vinniefalco/Beast/pull/654?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9odHRwL2RldGFpbC9iYXNpY19wYXJzZXIuaHBw) | `98.03% <100%> (ø)` | :arrow_up: |  
| [include/beast/websocket/impl/read.ipp](https://codecov.io/gh/vinniefalco/Beast/pull/654?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC93ZWJzb2NrZXQvaW1wbC9yZWFkLmlwcA==) | `86.9% <100%> (+0.05%)` | :arrow_up: |  
| ... and [9 more](https://codecov.io/gh/vinniefalco/Beast/pull/654?src=pr&el=tree-more) | |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/vinniefalco/Beast/pull/654?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/vinniefalco/Beast/pull/654?src=pr&el=footer). Last update [fde46eb...d3cb1c2](https://codecov.io/gh/vinniefalco/Beast/pull/654?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---

## Comment 2

> Username: vinniefalco  
> Created_at: 2017-07-16 14:27:04 UTC  
> Url: https://github.com/boostorg/beast/pull/654#issuecomment-315612658  

Merged

---
