# #519 Version 63 [Closed]

> Username: vinniefalco  
> Created at: 2017-06-21 00:50:02 UTC  
> Updated at: 2017-06-21 17:04:50 UTC  
> Closed at: 2017-06-21 17:04:29 UTC  
> Url: https://github.com/boostorg/beast/pull/519  

* Use std::to_string instead of lexical_cast  
* Put num_jobs back up on Travis  
* Fix BOOST_ROOT dir check when installing  
* Only build and run tests in variant=coverage  
* Move benchmarks to a separate project  
* Only run the tests under ubasan  
* Tidy up CMakeLists.txt  
* Tidy up Jamfiles  
* Control running with valgrind explicitly

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2017-06-21 01:10:50 UTC  
> Url: https://github.com/boostorg/beast/pull/519#issuecomment-309934117  

# [Codecov](https://codecov.io/gh/vinniefalco/Beast/pull/519?src=pr&el=h1) Report  
> Merging [#519](https://codecov.io/gh/vinniefalco/Beast/pull/519?src=pr&el=desc) into [master](https://codecov.io/gh/vinniefalco/Beast/commit/45d8b81b08b7b84a2de3f06b072984a528955743?src=pr&el=desc) will **increase** coverage by `0.44%`.  
> The diff coverage is `n/a`.  
  
[![Impacted file tree graph](https://codecov.io/gh/vinniefalco/Beast/pull/519/graphs/tree.svg?token=Gq6MFA9JRF&src=pr&width=650&height=150)](https://codecov.io/gh/vinniefalco/Beast/pull/519?src=pr&el=tree)  
  
```diff  
@@            Coverage Diff            @@  
##           master    #519      +/-   ##  
=========================================  
+ Coverage   93.06%   93.5%   +0.44%       
=========================================  
  Files          96      95       -1       
  Lines        7136    7097      -39       
=========================================  
- Hits         6641    6636       -5       
+ Misses        495     461      -34  
```  
  
  
| [Impacted Files](https://codecov.io/gh/vinniefalco/Beast/pull/519?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [include/beast/websocket/impl/write.ipp](https://codecov.io/gh/vinniefalco/Beast/pull/519?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC93ZWJzb2NrZXQvaW1wbC93cml0ZS5pcHA=) | `87.37% <0%> (-0.04%)` | :arrow_down: |  
| [include/beast/core/impl/read\_size.ipp](https://codecov.io/gh/vinniefalco/Beast/pull/519?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9jb3JlL2ltcGwvcmVhZF9zaXplLmlwcA==) | `100% <0%> (ø)` | :arrow_up: |  
| [include/beast/core/string\_param.hpp](https://codecov.io/gh/vinniefalco/Beast/pull/519?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9jb3JlL3N0cmluZ19wYXJhbS5ocHA=) | `100% <0%> (ø)` | :arrow_up: |  
| [include/beast/zlib/inflate\_stream.hpp](https://codecov.io/gh/vinniefalco/Beast/pull/519?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC96bGliL2luZmxhdGVfc3RyZWFtLmhwcA==) | `100% <0%> (ø)` | :arrow_up: |  
| [include/beast/websocket/impl/ssl.ipp](https://codecov.io/gh/vinniefalco/Beast/pull/519?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC93ZWJzb2NrZXQvaW1wbC9zc2wuaXBw) | | |  
| [include/beast/http/impl/write.ipp](https://codecov.io/gh/vinniefalco/Beast/pull/519?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9odHRwL2ltcGwvd3JpdGUuaXBw) | `91.89% <0%> (+0.44%)` | :arrow_up: |  
| [include/beast/websocket/impl/accept.ipp](https://codecov.io/gh/vinniefalco/Beast/pull/519?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC93ZWJzb2NrZXQvaW1wbC9hY2NlcHQuaXBw) | `100% <0%> (+0.5%)` | :arrow_up: |  
| [include/beast/http/detail/rfc7230.hpp](https://codecov.io/gh/vinniefalco/Beast/pull/519?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9odHRwL2RldGFpbC9yZmM3MjMwLmhwcA==) | `81.92% <0%> (+0.97%)` | :arrow_up: |  
| [include/beast/core/impl/buffered\_read\_stream.ipp](https://codecov.io/gh/vinniefalco/Beast/pull/519?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9jb3JlL2ltcGwvYnVmZmVyZWRfcmVhZF9zdHJlYW0uaXBw) | `100% <0%> (+1.53%)` | :arrow_up: |  
| [include/beast/http/parser.hpp](https://codecov.io/gh/vinniefalco/Beast/pull/519?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9odHRwL3BhcnNlci5ocHA=) | `97.05% <0%> (+2.77%)` | :arrow_up: |  
| ... and [2 more](https://codecov.io/gh/vinniefalco/Beast/pull/519?src=pr&el=tree-more) | |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/vinniefalco/Beast/pull/519?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/vinniefalco/Beast/pull/519?src=pr&el=footer). Last update [45d8b81...ed61dce](https://codecov.io/gh/vinniefalco/Beast/pull/519?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---

## Comment 2

> Username: codecov[bot]  
> Created_at: 2017-06-21 01:10:50 UTC  
> Updated_at: 2017-06-21 03:21:07 UTC  
> Url: https://github.com/boostorg/beast/pull/519#issuecomment-309934118  

# [Codecov](https://codecov.io/gh/vinniefalco/Beast/pull/519?src=pr&el=h1) Report  
> Merging [#519](https://codecov.io/gh/vinniefalco/Beast/pull/519?src=pr&el=desc) into [master](https://codecov.io/gh/vinniefalco/Beast/commit/45d8b81b08b7b84a2de3f06b072984a528955743?src=pr&el=desc) will **increase** coverage by `0.44%`.  
> The diff coverage is `n/a`.  
  
[![Impacted file tree graph](https://codecov.io/gh/vinniefalco/Beast/pull/519/graphs/tree.svg?width=650&src=pr&token=Gq6MFA9JRF&height=150)](https://codecov.io/gh/vinniefalco/Beast/pull/519?src=pr&el=tree)  
  
```diff  
@@            Coverage Diff            @@  
##           master    #519      +/-   ##  
=========================================  
+ Coverage   93.06%   93.5%   +0.44%       
=========================================  
  Files          96      95       -1       
  Lines        7136    7097      -39       
=========================================  
- Hits         6641    6636       -5       
+ Misses        495     461      -34  
```  
  
  
| [Impacted Files](https://codecov.io/gh/vinniefalco/Beast/pull/519?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [include/beast/websocket/impl/write.ipp](https://codecov.io/gh/vinniefalco/Beast/pull/519?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC93ZWJzb2NrZXQvaW1wbC93cml0ZS5pcHA=) | `87.37% <0%> (-0.04%)` | :arrow_down: |  
| [include/beast/zlib/inflate\_stream.hpp](https://codecov.io/gh/vinniefalco/Beast/pull/519?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC96bGliL2luZmxhdGVfc3RyZWFtLmhwcA==) | `100% <0%> (ø)` | :arrow_up: |  
| [include/beast/core/string\_param.hpp](https://codecov.io/gh/vinniefalco/Beast/pull/519?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9jb3JlL3N0cmluZ19wYXJhbS5ocHA=) | `100% <0%> (ø)` | :arrow_up: |  
| [include/beast/core/impl/read\_size.ipp](https://codecov.io/gh/vinniefalco/Beast/pull/519?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9jb3JlL2ltcGwvcmVhZF9zaXplLmlwcA==) | `100% <0%> (ø)` | :arrow_up: |  
| [include/beast/websocket/impl/ssl.ipp](https://codecov.io/gh/vinniefalco/Beast/pull/519?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC93ZWJzb2NrZXQvaW1wbC9zc2wuaXBw) | | |  
| [include/beast/http/impl/write.ipp](https://codecov.io/gh/vinniefalco/Beast/pull/519?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9odHRwL2ltcGwvd3JpdGUuaXBw) | `91.89% <0%> (+0.44%)` | :arrow_up: |  
| [include/beast/websocket/impl/accept.ipp](https://codecov.io/gh/vinniefalco/Beast/pull/519?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC93ZWJzb2NrZXQvaW1wbC9hY2NlcHQuaXBw) | `100% <0%> (+0.5%)` | :arrow_up: |  
| [include/beast/http/detail/rfc7230.hpp](https://codecov.io/gh/vinniefalco/Beast/pull/519?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9odHRwL2RldGFpbC9yZmM3MjMwLmhwcA==) | `81.92% <0%> (+0.97%)` | :arrow_up: |  
| [include/beast/core/impl/buffered\_read\_stream.ipp](https://codecov.io/gh/vinniefalco/Beast/pull/519?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9jb3JlL2ltcGwvYnVmZmVyZWRfcmVhZF9zdHJlYW0uaXBw) | `100% <0%> (+1.53%)` | :arrow_up: |  
| [include/beast/http/parser.hpp](https://codecov.io/gh/vinniefalco/Beast/pull/519?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9odHRwL3BhcnNlci5ocHA=) | `97.05% <0%> (+2.77%)` | :arrow_up: |  
| ... and [2 more](https://codecov.io/gh/vinniefalco/Beast/pull/519?src=pr&el=tree-more) | |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/vinniefalco/Beast/pull/519?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/vinniefalco/Beast/pull/519?src=pr&el=footer). Last update [45d8b81...56cc107](https://codecov.io/gh/vinniefalco/Beast/pull/519?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---

## Comment 3

> Username: vinniefalco  
> Created_at: 2017-06-21 17:04:29 UTC  
> Url: https://github.com/boostorg/beast/pull/519#issuecomment-310143056  

Merged

---
