# #465 Version 54 [Merged]

> Username: vinniefalco  
> Created at: 2017-06-11 02:47:45 UTC  
> Updated at: 2017-06-12 03:23:37 UTC  
> Merged at: 2017-06-12 02:47:41 UTC  
> Closed at: 2017-06-12 02:47:41 UTC  
> Url: https://github.com/boostorg/beast/pull/465  

* static_buffer coverage  
* flat_buffer coverage  
* multi_buffer coverage  
* consuming_buffers members and coverage  
* basic_fields members and coverage  
* Add string_param  
* Retain ownership when reading using a message  
* Fix incorrect use of [[fallthrough]]  
  
API Changes:  
  
* basic_fields refactor

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2017-06-11 04:11:02 UTC  
> Updated_at: 2017-06-12 03:23:37 UTC  
> Url: https://github.com/boostorg/beast/pull/465#issuecomment-307605132  

# [Codecov](https://codecov.io/gh/vinniefalco/Beast/pull/465?src=pr&el=h1) Report  
> Merging [#465](https://codecov.io/gh/vinniefalco/Beast/pull/465?src=pr&el=desc) into [master](https://codecov.io/gh/vinniefalco/Beast/commit/18a8ef5a3b015e1817c5528d30f4f07266321efd?src=pr&el=desc) will **increase** coverage by `0.59%`.  
> The diff coverage is `93.33%`.  
  
[![Impacted file tree graph](https://codecov.io/gh/vinniefalco/Beast/pull/465/graphs/tree.svg?token=Gq6MFA9JRF&width=650&src=pr&height=150)](https://codecov.io/gh/vinniefalco/Beast/pull/465?src=pr&el=tree)  
  
```diff  
@@            Coverage Diff             @@  
##           master     #465      +/-   ##  
==========================================  
+ Coverage   93.19%   93.78%   +0.59%       
==========================================  
  Files          92       95       +3       
  Lines        6741     6939     +198       
==========================================  
+ Hits         6282     6508     +226       
+ Misses        459      431      -28  
```  
  
  
| [Impacted Files](https://codecov.io/gh/vinniefalco/Beast/pull/465?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [include/beast/core/detail/read\_size\_helper.hpp](https://codecov.io/gh/vinniefalco/Beast/pull/465?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9jb3JlL2RldGFpbC9yZWFkX3NpemVfaGVscGVyLmhwcA==) | `90.9% <ø> (+6.29%)` | :arrow_up: |  
| [include/beast/core/async\_result.hpp](https://codecov.io/gh/vinniefalco/Beast/pull/465?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9jb3JlL2FzeW5jX3Jlc3VsdC5ocHA=) | `100% <ø> (ø)` | :arrow_up: |  
| [include/beast/websocket/stream.hpp](https://codecov.io/gh/vinniefalco/Beast/pull/465?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC93ZWJzb2NrZXQvc3RyZWFtLmhwcA==) | `90.47% <ø> (ø)` | :arrow_up: |  
| [include/beast/core/static\_buffer.hpp](https://codecov.io/gh/vinniefalco/Beast/pull/465?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9jb3JlL3N0YXRpY19idWZmZXIuaHBw) | `100% <ø> (ø)` | :arrow_up: |  
| [include/beast/http/message.hpp](https://codecov.io/gh/vinniefalco/Beast/pull/465?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9odHRwL21lc3NhZ2UuaHBw) | `100% <ø> (ø)` | :arrow_up: |  
| [include/beast/core/flat\_buffer.hpp](https://codecov.io/gh/vinniefalco/Beast/pull/465?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9jb3JlL2ZsYXRfYnVmZmVyLmhwcA==) | `100% <ø> (ø)` | :arrow_up: |  
| [include/beast/core/multi\_buffer.hpp](https://codecov.io/gh/vinniefalco/Beast/pull/465?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9jb3JlL211bHRpX2J1ZmZlci5ocHA=) | `100% <ø> (ø)` | |  
| [include/beast/core/consuming\_buffers.hpp](https://codecov.io/gh/vinniefalco/Beast/pull/465?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9jb3JlL2NvbnN1bWluZ19idWZmZXJzLmhwcA==) | `100% <ø> (ø)` | :arrow_up: |  
| [include/beast/websocket/impl/stream.ipp](https://codecov.io/gh/vinniefalco/Beast/pull/465?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC93ZWJzb2NrZXQvaW1wbC9zdHJlYW0uaXBw) | `95.13% <100%> (-0.04%)` | :arrow_down: |  
| [include/beast/http/fields.hpp](https://codecov.io/gh/vinniefalco/Beast/pull/465?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9odHRwL2ZpZWxkcy5ocHA=) | `100% <100%> (+20%)` | :arrow_up: |  
| ... and [27 more](https://codecov.io/gh/vinniefalco/Beast/pull/465?src=pr&el=tree-more) | |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/vinniefalco/Beast/pull/465?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/vinniefalco/Beast/pull/465?src=pr&el=footer). Last update [18a8ef5...6a8912a](https://codecov.io/gh/vinniefalco/Beast/pull/465?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---
