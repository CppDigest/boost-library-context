# #444 Set version to 51 [Merged]

> Username: vinniefalco  
> Created at: 2017-06-08 21:44:02 UTC  
> Updated at: 2017-06-09 22:41:38 UTC  
> Merged at: 2017-06-09 22:07:47 UTC  
> Closed at: 2017-06-09 22:07:47 UTC  
> Url: https://github.com/boostorg/beast/pull/444  

* Fix operator<< for header  
* Tidy up file_body  
* Fix file_body::get() not setting the more flag correctly  
* Use BOOST_FALLTHROUGH  
* Use BOOST_STRINGIZE  
* DynamicBuffer benchmarks  
  
API Changes:  
  
* Tune up static_buffer  
* multi_buffer implementation change   
  
**Action Required:**  
  
* Call sites passing a number to multi_buffer's constructor  
  will need to be adjusted, see the corresponding commit message.  
  
Documentation preview:  
http://vinniefalco.github.io/stage/beast/v51/

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2017-06-08 22:30:20 UTC  
> Url: https://github.com/boostorg/beast/pull/444#issuecomment-307245064  

# [Codecov](https://codecov.io/gh/vinniefalco/Beast/pull/444?src=pr&el=h1) Report  
> Merging [#444](https://codecov.io/gh/vinniefalco/Beast/pull/444?src=pr&el=desc) into [master](https://codecov.io/gh/vinniefalco/Beast/commit/a2c1117fc0c8125f88d5cfcf14e91f9963441e45?src=pr&el=desc) will **decrease** coverage by `0.43%`.  
> The diff coverage is `85.32%`.  
  
[![Impacted file tree graph](https://codecov.io/gh/vinniefalco/Beast/pull/444/graphs/tree.svg?width=650&height=150&src=pr&token=Gq6MFA9JRF)](https://codecov.io/gh/vinniefalco/Beast/pull/444?src=pr&el=tree)  
  
```diff  
@@            Coverage Diff             @@  
##           master     #444      +/-   ##  
==========================================  
- Coverage   93.79%   93.36%   -0.44%       
==========================================  
  Files          92       91       -1       
  Lines        6723     6719       -4       
==========================================  
- Hits         6306     6273      -33       
- Misses        417      446      +29  
```  
  
  
| [Impacted Files](https://codecov.io/gh/vinniefalco/Beast/pull/444?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [include/beast/websocket/impl/accept.ipp](https://codecov.io/gh/vinniefalco/Beast/pull/444?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC93ZWJzb2NrZXQvaW1wbC9hY2NlcHQuaXBw) | `100% <ø> (ø)` | :arrow_up: |  
| [include/beast/websocket/impl/close.ipp](https://codecov.io/gh/vinniefalco/Beast/pull/444?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC93ZWJzb2NrZXQvaW1wbC9jbG9zZS5pcHA=) | `97.36% <ø> (ø)` | :arrow_up: |  
| [include/beast/websocket/impl/ping.ipp](https://codecov.io/gh/vinniefalco/Beast/pull/444?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC93ZWJzb2NrZXQvaW1wbC9waW5nLmlwcA==) | `93.25% <ø> (ø)` | :arrow_up: |  
| [include/beast/websocket/impl/handshake.ipp](https://codecov.io/gh/vinniefalco/Beast/pull/444?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC93ZWJzb2NrZXQvaW1wbC9oYW5kc2hha2UuaXBw) | `96.59% <ø> (ø)` | :arrow_up: |  
| [include/beast/http/impl/basic\_parser.ipp](https://codecov.io/gh/vinniefalco/Beast/pull/444?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9odHRwL2ltcGwvYmFzaWNfcGFyc2VyLmlwcA==) | `98.96% <ø> (ø)` | :arrow_up: |  
| [include/beast/http/impl/verb.ipp](https://codecov.io/gh/vinniefalco/Beast/pull/444?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9odHRwL2ltcGwvdmVyYi5pcHA=) | `100% <ø> (ø)` | :arrow_up: |  
| [include/beast/http/impl/status.ipp](https://codecov.io/gh/vinniefalco/Beast/pull/444?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9odHRwL2ltcGwvc3RhdHVzLmlwcA==) | `100% <ø> (ø)` | :arrow_up: |  
| [include/beast/core/impl/buffers\_adapter.ipp](https://codecov.io/gh/vinniefalco/Beast/pull/444?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9jb3JlL2ltcGwvYnVmZmVyc19hZGFwdGVyLmlwcA==) | `95.55% <ø> (-4.45%)` | :arrow_down: |  
| [include/beast/http/impl/read.ipp](https://codecov.io/gh/vinniefalco/Beast/pull/444?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9odHRwL2ltcGwvcmVhZC5pcHA=) | `89.87% <ø> (ø)` | :arrow_up: |  
| [include/beast/http/impl/serializer.ipp](https://codecov.io/gh/vinniefalco/Beast/pull/444?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9odHRwL2ltcGwvc2VyaWFsaXplci5pcHA=) | `95.2% <ø> (ø)` | :arrow_up: |  
| ... and [14 more](https://codecov.io/gh/vinniefalco/Beast/pull/444?src=pr&el=tree-more) | |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/vinniefalco/Beast/pull/444?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/vinniefalco/Beast/pull/444?src=pr&el=footer). Last update [a2c1117...2f07ce5](https://codecov.io/gh/vinniefalco/Beast/pull/444?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---

## Comment 2

> Username: codecov[bot]  
> Created_at: 2017-06-08 22:30:20 UTC  
> Updated_at: 2017-06-09 22:41:38 UTC  
> Url: https://github.com/boostorg/beast/pull/444#issuecomment-307245066  

# [Codecov](https://codecov.io/gh/vinniefalco/Beast/pull/444?src=pr&el=h1) Report  
> Merging [#444](https://codecov.io/gh/vinniefalco/Beast/pull/444?src=pr&el=desc) into [master](https://codecov.io/gh/vinniefalco/Beast/commit/a2c1117fc0c8125f88d5cfcf14e91f9963441e45?src=pr&el=desc) will **decrease** coverage by `0.43%`.  
> The diff coverage is `85.45%`.  
  
[![Impacted file tree graph](https://codecov.io/gh/vinniefalco/Beast/pull/444/graphs/tree.svg?height=150&token=Gq6MFA9JRF&width=650&src=pr)](https://codecov.io/gh/vinniefalco/Beast/pull/444?src=pr&el=tree)  
  
```diff  
@@            Coverage Diff             @@  
##           master     #444      +/-   ##  
==========================================  
- Coverage   93.79%   93.36%   -0.44%       
==========================================  
  Files          92       91       -1       
  Lines        6723     6719       -4       
==========================================  
- Hits         6306     6273      -33       
- Misses        417      446      +29  
```  
  
  
| [Impacted Files](https://codecov.io/gh/vinniefalco/Beast/pull/444?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [include/beast/http/impl/status.ipp](https://codecov.io/gh/vinniefalco/Beast/pull/444?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9odHRwL2ltcGwvc3RhdHVzLmlwcA==) | `100% <ø> (ø)` | :arrow_up: |  
| [include/beast/http/impl/read.ipp](https://codecov.io/gh/vinniefalco/Beast/pull/444?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9odHRwL2ltcGwvcmVhZC5pcHA=) | `89.87% <ø> (ø)` | :arrow_up: |  
| [include/beast/http/impl/verb.ipp](https://codecov.io/gh/vinniefalco/Beast/pull/444?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9odHRwL2ltcGwvdmVyYi5pcHA=) | `100% <ø> (ø)` | :arrow_up: |  
| [include/beast/websocket/impl/accept.ipp](https://codecov.io/gh/vinniefalco/Beast/pull/444?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC93ZWJzb2NrZXQvaW1wbC9hY2NlcHQuaXBw) | `100% <ø> (ø)` | :arrow_up: |  
| [include/beast/websocket/impl/handshake.ipp](https://codecov.io/gh/vinniefalco/Beast/pull/444?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC93ZWJzb2NrZXQvaW1wbC9oYW5kc2hha2UuaXBw) | `96.59% <ø> (ø)` | :arrow_up: |  
| [include/beast/websocket/impl/close.ipp](https://codecov.io/gh/vinniefalco/Beast/pull/444?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC93ZWJzb2NrZXQvaW1wbC9jbG9zZS5pcHA=) | `97.36% <ø> (ø)` | :arrow_up: |  
| [include/beast/http/impl/serializer.ipp](https://codecov.io/gh/vinniefalco/Beast/pull/444?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9odHRwL2ltcGwvc2VyaWFsaXplci5pcHA=) | `95.2% <ø> (ø)` | :arrow_up: |  
| [include/beast/http/impl/write.ipp](https://codecov.io/gh/vinniefalco/Beast/pull/444?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9odHRwL2ltcGwvd3JpdGUuaXBw) | `93.25% <ø> (ø)` | :arrow_up: |  
| [include/beast/websocket/impl/ping.ipp](https://codecov.io/gh/vinniefalco/Beast/pull/444?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC93ZWJzb2NrZXQvaW1wbC9waW5nLmlwcA==) | `93.25% <ø> (ø)` | :arrow_up: |  
| [include/beast/core/impl/buffers\_adapter.ipp](https://codecov.io/gh/vinniefalco/Beast/pull/444?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9jb3JlL2ltcGwvYnVmZmVyc19hZGFwdGVyLmlwcA==) | `95.55% <ø> (-4.45%)` | :arrow_down: |  
| ... and [14 more](https://codecov.io/gh/vinniefalco/Beast/pull/444?src=pr&el=tree-more) | |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/vinniefalco/Beast/pull/444?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/vinniefalco/Beast/pull/444?src=pr&el=footer). Last update [a2c1117...13c64e3](https://codecov.io/gh/vinniefalco/Beast/pull/444?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---
