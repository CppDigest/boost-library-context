# #576 Version 71 [Closed]

> Username: vinniefalco  
> Created at: 2017-07-02 18:35:28 UTC  
> Updated at: 2017-07-04 00:52:10 UTC  
> Closed at: 2017-07-04 00:52:10 UTC  
> Url: https://github.com/boostorg/beast/pull/576  

* Fix extra ; warning  
* Documentation revision  
* Fix spurious on_chunk invocation  
* Call prepare_payload in HTTP example  
* Check trailers in test  
* Fix buffer overflow handling for string_body and mutable_body  
* Concept check in basic_dynamic_body  
* Tidy up http_sync_port error check  
  
WebSockets:  
  
* Fine tune websocket op asserts  
* Refactor websocket composed ops  
* Allow close, ping, and write to happen concurrently  
* Fix race in websocket read op  
* Fix websocket write op  
* Add cmake options for examples and tests  
  
API Changes:  
  
* Return `std::size_t` from `Body::writer::put`  
  
Actions Required:  
  
* Return the number of bytes actually transferred from the input buffers in user defined `Body::writer::put` functions.

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2017-07-02 19:06:09 UTC  
> Url: https://github.com/boostorg/beast/pull/576#issuecomment-312510643  

# [Codecov](https://codecov.io/gh/vinniefalco/Beast/pull/576?src=pr&el=h1) Report  
> Merging [#576](https://codecov.io/gh/vinniefalco/Beast/pull/576?src=pr&el=desc) into [develop](https://codecov.io/gh/vinniefalco/Beast/commit/78a065ba39836d91d7e70d93de7f9140f518083b?src=pr&el=desc) will **increase** coverage by `<.01%`.  
> The diff coverage is `82.62%`.  
  
[![Impacted file tree graph](https://codecov.io/gh/vinniefalco/Beast/pull/576/graphs/tree.svg?width=650&token=Gq6MFA9JRF&height=150&src=pr)](https://codecov.io/gh/vinniefalco/Beast/pull/576?src=pr&el=tree)  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #576      +/-   ##  
===========================================  
+ Coverage    93.77%   93.78%   +<.01%       
===========================================  
  Files           94       94                
  Lines         7393     7368      -25       
===========================================  
- Hits          6933     6910      -23       
+ Misses         460      458       -2  
```  
  
  
| [Impacted Files](https://codecov.io/gh/vinniefalco/Beast/pull/576?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [include/beast/http/basic\_parser.hpp](https://codecov.io/gh/vinniefalco/Beast/pull/576?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9odHRwL2Jhc2ljX3BhcnNlci5ocHA=) | `100% <ø> (ø)` | :arrow_up: |  
| [include/beast/websocket/stream.hpp](https://codecov.io/gh/vinniefalco/Beast/pull/576?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC93ZWJzb2NrZXQvc3RyZWFtLmhwcA==) | `91.66% <ø> (ø)` | :arrow_up: |  
| [include/beast/http/empty\_body.hpp](https://codecov.io/gh/vinniefalco/Beast/pull/576?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9odHRwL2VtcHR5X2JvZHkuaHBw) | `57.14% <ø> (ø)` | :arrow_up: |  
| [include/beast/http/impl/basic\_parser.ipp](https://codecov.io/gh/vinniefalco/Beast/pull/576?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9odHRwL2ltcGwvYmFzaWNfcGFyc2VyLmlwcA==) | `95.73% <100%> (-0.03%)` | :arrow_down: |  
| [include/beast/http/parser.hpp](https://codecov.io/gh/vinniefalco/Beast/pull/576?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9odHRwL3BhcnNlci5ocHA=) | `80.48% <100%> (ø)` | :arrow_up: |  
| [include/beast/http/string\_body.hpp](https://codecov.io/gh/vinniefalco/Beast/pull/576?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9odHRwL3N0cmluZ19ib2R5LmhwcA==) | `85.18% <57.14%> (-7.13%)` | :arrow_down: |  
| [include/beast/websocket/impl/read.ipp](https://codecov.io/gh/vinniefalco/Beast/pull/576?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC93ZWJzb2NrZXQvaW1wbC9yZWFkLmlwcA==) | `86.84% <68.75%> (+0.19%)` | :arrow_up: |  
| [include/beast/http/buffer\_body.hpp](https://codecov.io/gh/vinniefalco/Beast/pull/576?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9odHRwL2J1ZmZlcl9ib2R5LmhwcA==) | `90.62% <75%> (-6.05%)` | :arrow_down: |  
| [include/beast/http/dynamic\_body.hpp](https://codecov.io/gh/vinniefalco/Beast/pull/576?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9odHRwL2R5bmFtaWNfYm9keS5ocHA=) | `82.6% <77.77%> (-4.9%)` | :arrow_down: |  
| [include/beast/websocket/impl/write.ipp](https://codecov.io/gh/vinniefalco/Beast/pull/576?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC93ZWJzb2NrZXQvaW1wbC93cml0ZS5pcHA=) | `87.5% <81.35%> (+0.43%)` | :arrow_up: |  
| ... and [5 more](https://codecov.io/gh/vinniefalco/Beast/pull/576?src=pr&el=tree-more) | |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/vinniefalco/Beast/pull/576?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/vinniefalco/Beast/pull/576?src=pr&el=footer). Last update [78a065b...e5718f3](https://codecov.io/gh/vinniefalco/Beast/pull/576?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---

## Comment 2

> Username: codecov[bot]  
> Created_at: 2017-07-02 19:06:10 UTC  
> Updated_at: 2017-07-04 00:12:41 UTC  
> Url: https://github.com/boostorg/beast/pull/576#issuecomment-312510644  

# [Codecov](https://codecov.io/gh/vinniefalco/Beast/pull/576?src=pr&el=h1) Report  
> Merging [#576](https://codecov.io/gh/vinniefalco/Beast/pull/576?src=pr&el=desc) into [develop](https://codecov.io/gh/vinniefalco/Beast/commit/78a065ba39836d91d7e70d93de7f9140f518083b?src=pr&el=desc) will **increase** coverage by `0.04%`.  
> The diff coverage is `89.68%`.  
  
[![Impacted file tree graph](https://codecov.io/gh/vinniefalco/Beast/pull/576/graphs/tree.svg?token=Gq6MFA9JRF&src=pr&width=650&height=150)](https://codecov.io/gh/vinniefalco/Beast/pull/576?src=pr&el=tree)  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #576      +/-   ##  
===========================================  
+ Coverage    93.77%   93.81%   +0.04%       
===========================================  
  Files           94       94                
  Lines         7393     7459      +66       
===========================================  
+ Hits          6933     6998      +65       
- Misses         460      461       +1  
```  
  
  
| [Impacted Files](https://codecov.io/gh/vinniefalco/Beast/pull/576?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [include/beast/http/message.hpp](https://codecov.io/gh/vinniefalco/Beast/pull/576?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9odHRwL21lc3NhZ2UuaHBw) | `100% <ø> (ø)` | :arrow_up: |  
| [include/beast/websocket/stream.hpp](https://codecov.io/gh/vinniefalco/Beast/pull/576?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC93ZWJzb2NrZXQvc3RyZWFtLmhwcA==) | `91.66% <ø> (ø)` | :arrow_up: |  
| [include/beast/http/empty\_body.hpp](https://codecov.io/gh/vinniefalco/Beast/pull/576?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9odHRwL2VtcHR5X2JvZHkuaHBw) | `57.14% <ø> (ø)` | :arrow_up: |  
| [include/beast/http/detail/rfc7230.hpp](https://codecov.io/gh/vinniefalco/Beast/pull/576?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9odHRwL2RldGFpbC9yZmM3MjMwLmhwcA==) | `80.76% <ø> (-0.25%)` | :arrow_down: |  
| [include/beast/http/parser.hpp](https://codecov.io/gh/vinniefalco/Beast/pull/576?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9odHRwL3BhcnNlci5ocHA=) | `80.48% <100%> (ø)` | :arrow_up: |  
| [include/beast/http/basic\_parser.hpp](https://codecov.io/gh/vinniefalco/Beast/pull/576?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9odHRwL2Jhc2ljX3BhcnNlci5ocHA=) | `100% <100%> (ø)` | :arrow_up: |  
| [include/beast/http/string\_body.hpp](https://codecov.io/gh/vinniefalco/Beast/pull/576?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9odHRwL3N0cmluZ19ib2R5LmhwcA==) | `85.18% <57.14%> (-7.13%)` | :arrow_down: |  
| [include/beast/websocket/impl/read.ipp](https://codecov.io/gh/vinniefalco/Beast/pull/576?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC93ZWJzb2NrZXQvaW1wbC9yZWFkLmlwcA==) | `86.84% <68.75%> (+0.19%)` | :arrow_up: |  
| [include/beast/http/buffer\_body.hpp](https://codecov.io/gh/vinniefalco/Beast/pull/576?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9odHRwL2J1ZmZlcl9ib2R5LmhwcA==) | `90.62% <75%> (-6.05%)` | :arrow_down: |  
| [include/beast/http/dynamic\_body.hpp](https://codecov.io/gh/vinniefalco/Beast/pull/576?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9odHRwL2R5bmFtaWNfYm9keS5ocHA=) | `82.6% <77.77%> (-4.9%)` | :arrow_down: |  
| ... and [11 more](https://codecov.io/gh/vinniefalco/Beast/pull/576?src=pr&el=tree-more) | |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/vinniefalco/Beast/pull/576?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/vinniefalco/Beast/pull/576?src=pr&el=footer). Last update [78a065b...fa6e663](https://codecov.io/gh/vinniefalco/Beast/pull/576?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---

## Comment 3

> Username: vinniefalco  
> Created_at: 2017-07-04 00:52:10 UTC  
> Url: https://github.com/boostorg/beast/pull/576#issuecomment-312755878  

merged

---
