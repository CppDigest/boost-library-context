# #362 Version 41 [Merged]

> Username: vinniefalco  
> Created at: 2017-05-13 03:36:16 UTC  
> Updated at: 2017-05-14 23:27:14 UTC  
> Merged at: 2017-05-14 23:01:52 UTC  
> Closed at: 2017-05-14 23:01:52 UTC  
> Url: https://github.com/boostorg/beast/pull/362  

* Trim Appveyor matrix rows  
* Concept revision and documentation  
* Remove coveralls integration  
* Tidy up formal parameter names  
  
WebSocket  
  
* Tidy up websocket::close_code enum and constructors  
  
API Changes  
  
* Return http::error::end_of_stream on HTTP read eof  
* Remove placeholders  
* Rename prepare_buffer(s) to buffer_prefix  
* Remove handler helpers, tidy up hook invocations  
  
Documentation preview: http://vinniefalco.github.io/stage/beast/v41/

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2017-05-13 03:59:37 UTC  
> Updated_at: 2017-05-14 23:26:28 UTC  
> Url: https://github.com/boostorg/beast/pull/362#issuecomment-301223340  

# [Codecov](https://codecov.io/gh/vinniefalco/Beast/pull/362?src=pr&el=h1) Report  
> Merging [#362](https://codecov.io/gh/vinniefalco/Beast/pull/362?src=pr&el=desc) into [master](https://codecov.io/gh/vinniefalco/Beast/commit/f2d825594ee34ccc1ebc0b231899a1735245778d?src=pr&el=desc) will **increase** coverage by `<.01%`.  
> The diff coverage is `96.22%`.  
  
[![Impacted file tree graph](https://codecov.io/gh/vinniefalco/Beast/pull/362/graphs/tree.svg?token=Gq6MFA9JRF&src=pr&height=150&width=650)](https://codecov.io/gh/vinniefalco/Beast/pull/362?src=pr&el=tree)  
  
```diff  
@@            Coverage Diff             @@  
##           master     #362      +/-   ##  
==========================================  
+ Coverage   93.35%   93.36%   +<.01%       
==========================================  
  Files          93       91       -2       
  Lines        6534     6584      +50       
==========================================  
+ Hits         6100     6147      +47       
- Misses        434      437       +3  
```  
  
  
| [Impacted Files](https://codecov.io/gh/vinniefalco/Beast/pull/362?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [include/beast/core/impl/consuming\_buffers.ipp](https://codecov.io/gh/vinniefalco/Beast/pull/362?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9jb3JlL2ltcGwvY29uc3VtaW5nX2J1ZmZlcnMuaXBw) | `100% <ø> (ø)` | :arrow_up: |  
| [include/beast/core/detail/buffer\_cat.hpp](https://codecov.io/gh/vinniefalco/Beast/pull/362?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9jb3JlL2RldGFpbC9idWZmZXJfY2F0LmhwcA==) | `97.32% <ø> (ø)` | :arrow_up: |  
| [include/beast/core/static\_buffer.hpp](https://codecov.io/gh/vinniefalco/Beast/pull/362?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9jb3JlL3N0YXRpY19idWZmZXIuaHBw) | `100% <ø> (ø)` | :arrow_up: |  
| [include/beast/core/handler\_ptr.hpp](https://codecov.io/gh/vinniefalco/Beast/pull/362?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9jb3JlL2hhbmRsZXJfcHRyLmhwcA==) | `100% <ø> (ø)` | :arrow_up: |  
| [include/beast/core/detail/type\_traits.hpp](https://codecov.io/gh/vinniefalco/Beast/pull/362?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9jb3JlL2RldGFpbC90eXBlX3RyYWl0cy5ocHA=) | `100% <ø> (ø)` | :arrow_up: |  
| [include/beast/http/dynamic\_body.hpp](https://codecov.io/gh/vinniefalco/Beast/pull/362?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9odHRwL2R5bmFtaWNfYm9keS5ocHA=) | `100% <ø> (ø)` | :arrow_up: |  
| [include/beast/core/multi\_buffer.hpp](https://codecov.io/gh/vinniefalco/Beast/pull/362?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9jb3JlL211bHRpX2J1ZmZlci5ocHA=) | `100% <ø> (ø)` | :arrow_up: |  
| [include/beast/core/buffered\_read\_stream.hpp](https://codecov.io/gh/vinniefalco/Beast/pull/362?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9jb3JlL2J1ZmZlcmVkX3JlYWRfc3RyZWFtLmhwcA==) | `100% <ø> (ø)` | :arrow_up: |  
| [include/beast/core/buffer\_cat.hpp](https://codecov.io/gh/vinniefalco/Beast/pull/362?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9jb3JlL2J1ZmZlcl9jYXQuaHBw) | `100% <ø> (ø)` | :arrow_up: |  
| [include/beast/core/bind\_handler.hpp](https://codecov.io/gh/vinniefalco/Beast/pull/362?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9jb3JlL2JpbmRfaGFuZGxlci5ocHA=) | `100% <ø> (ø)` | :arrow_up: |  
| ... and [29 more](https://codecov.io/gh/vinniefalco/Beast/pull/362?src=pr&el=tree-more) | |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/vinniefalco/Beast/pull/362?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/vinniefalco/Beast/pull/362?src=pr&el=footer). Last update [f2d8255...823aee2](https://codecov.io/gh/vinniefalco/Beast/pull/362?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---
