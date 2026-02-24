# #517 Version 62 [Merged]

> Username: vinniefalco  
> Created at: 2017-06-20 03:20:00 UTC  
> Updated at: 2017-06-20 22:39:34 UTC  
> Merged at: 2017-06-20 21:54:44 UTC  
> Closed at: 2017-06-20 21:54:44 UTC  
> Url: https://github.com/boostorg/beast/pull/517  

* Remove libssl-dev from a Travis matrix item  
* Increase detail::static_ostream coverage  
* Add server-framework tests  
* Doc fixes and tidy  
* Tidy up namespaces in examples  
* Clear the error faster  
* Avoid explicit operator bool for error  
* Add http::is_fields trait  
* Squelch harmless not_connected errors  
  
API Changes:  
  
* parser requires basic_fields  
* Refine FieldsReader concept  
* message::prepare_payload replaces message::prepare  
  
Actions Required:  
  
* Callers using `parser` with Fields types other than basic_fields  
  will need to create their own subclass of basic_parser to work  
  with their custom fields type.  
  
* Implement chunked() and keep_alive() for user defined FieldsReader types.  
  
* Change calls to msg.prepare to msg.prepare_payload. For messages  
  with a user-defined Fields, provide the function prepare_payload_impl  
  in the fields type according to the Fields requirements.  
  
Documentation preview:  
http://vinniefalco.github.io/stage/beast/v62/

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2017-06-20 04:59:39 UTC  
> Updated_at: 2017-06-20 22:39:34 UTC  
> Url: https://github.com/boostorg/beast/pull/517#issuecomment-309645111  

# [Codecov](https://codecov.io/gh/vinniefalco/Beast/pull/517?src=pr&el=h1) Report  
> Merging [#517](https://codecov.io/gh/vinniefalco/Beast/pull/517?src=pr&el=desc) into [master](https://codecov.io/gh/vinniefalco/Beast/commit/4c15db48488cf292af76a8f4509686306b76449f?src=pr&el=desc) will **increase** coverage by `9.64%`.  
> The diff coverage is `80.82%`.  
  
[![Impacted file tree graph](https://codecov.io/gh/vinniefalco/Beast/pull/517/graphs/tree.svg?token=Gq6MFA9JRF&height=150&src=pr&width=650)](https://codecov.io/gh/vinniefalco/Beast/pull/517?src=pr&el=tree)  
  
```diff  
@@            Coverage Diff             @@  
##           master     #517      +/-   ##  
==========================================  
+ Coverage   83.41%   93.06%   +9.64%       
==========================================  
  Files          96       96                
  Lines        7098     7136      +38       
==========================================  
+ Hits         5921     6641     +720       
+ Misses       1177      495     -682  
```  
  
  
| [Impacted Files](https://codecov.io/gh/vinniefalco/Beast/pull/517?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [include/beast/websocket/stream.hpp](https://codecov.io/gh/vinniefalco/Beast/pull/517?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC93ZWJzb2NrZXQvc3RyZWFtLmhwcA==) | `90.47% <ø> (ø)` | :arrow_up: |  
| [include/beast/zlib/detail/deflate\_stream.hpp](https://codecov.io/gh/vinniefalco/Beast/pull/517?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC96bGliL2RldGFpbC9kZWZsYXRlX3N0cmVhbS5ocHA=) | `92.81% <ø> (+23.17%)` | :arrow_up: |  
| [include/beast/websocket/rfc6455.hpp](https://codecov.io/gh/vinniefalco/Beast/pull/517?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC93ZWJzb2NrZXQvcmZjNjQ1NS5ocHA=) | `100% <ø> (ø)` | :arrow_up: |  
| [include/beast/http/fields.hpp](https://codecov.io/gh/vinniefalco/Beast/pull/517?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9odHRwL2ZpZWxkcy5ocHA=) | `100% <ø> (ø)` | :arrow_up: |  
| [include/beast/http/dynamic\_body.hpp](https://codecov.io/gh/vinniefalco/Beast/pull/517?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9odHRwL2R5bmFtaWNfYm9keS5ocHA=) | `85.71% <100%> (ø)` | :arrow_up: |  
| [include/beast/websocket/impl/rfc6455.ipp](https://codecov.io/gh/vinniefalco/Beast/pull/517?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC93ZWJzb2NrZXQvaW1wbC9yZmM2NDU1LmlwcA==) | `100% <100%> (ø)` | :arrow_up: |  
| [include/beast/http/string\_body.hpp](https://codecov.io/gh/vinniefalco/Beast/pull/517?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9odHRwL3N0cmluZ19ib2R5LmhwcA==) | `90.9% <100%> (-0.4%)` | :arrow_down: |  
| [include/beast/http/string\_view\_body.hpp](https://codecov.io/gh/vinniefalco/Beast/pull/517?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9odHRwL3N0cmluZ192aWV3X2JvZHkuaHBw) | `100% <100%> (ø)` | :arrow_up: |  
| [include/beast/http/impl/message.ipp](https://codecov.io/gh/vinniefalco/Beast/pull/517?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9odHRwL2ltcGwvbWVzc2FnZS5pcHA=) | `90.9% <100%> (+1.57%)` | :arrow_up: |  
| [include/beast/websocket/impl/accept.ipp](https://codecov.io/gh/vinniefalco/Beast/pull/517?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC93ZWJzb2NrZXQvaW1wbC9hY2NlcHQuaXBw) | `99.49% <100%> (ø)` | :arrow_up: |  
| ... and [39 more](https://codecov.io/gh/vinniefalco/Beast/pull/517?src=pr&el=tree-more) | |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/vinniefalco/Beast/pull/517?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/vinniefalco/Beast/pull/517?src=pr&el=footer). Last update [4c15db4...45d8b81](https://codecov.io/gh/vinniefalco/Beast/pull/517?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---
