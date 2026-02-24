# #541 Version 68 [Merged]

> Username: vinniefalco  
> Created at: 2017-06-25 19:41:34 UTC  
> Updated at: 2017-06-27 03:32:32 UTC  
> Merged at: 2017-06-27 02:49:08 UTC  
> Closed at: 2017-06-27 02:49:08 UTC  
> Url: https://github.com/boostorg/beast/pull/541  

* Split common tests to a new project  
* Small speed up in fields comparisons  
* Adjust buffer size in fast server  
* Use string_ref in older Boost versions  
  
API Changes:  
  
* Change BodyReader, BodyWriter requirements  
* Remove BodyReader::is_deferred  
* http::error::bad_target replaces bad_path  
  
Actions Required:  
  
* Change user defined instances of BodyReader and BodyWriter  
  to meet the new requirements.  
  
* Replace references to http::error::bad_path with http::error::bad_target  
  
Documentation preview:  
http://vinniefalco.github.io/stage/beast/v68/

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2017-06-25 21:02:22 UTC  
> Updated_at: 2017-06-27 03:32:32 UTC  
> Url: https://github.com/boostorg/beast/pull/541#issuecomment-310928120  

# [Codecov](https://codecov.io/gh/vinniefalco/Beast/pull/541?src=pr&el=h1) Report  
> Merging [#541](https://codecov.io/gh/vinniefalco/Beast/pull/541?src=pr&el=desc) into [master](https://codecov.io/gh/vinniefalco/Beast/commit/8982e14aa65b9922ac5a00e5a5196a08dfa8f29e?src=pr&el=desc) will **increase** coverage by `0.07%`.  
> The diff coverage is `100%`.  
  
[![Impacted file tree graph](https://codecov.io/gh/vinniefalco/Beast/pull/541/graphs/tree.svg?src=pr&token=Gq6MFA9JRF&width=650&height=150)](https://codecov.io/gh/vinniefalco/Beast/pull/541?src=pr&el=tree)  
  
```diff  
@@            Coverage Diff             @@  
##           master     #541      +/-   ##  
==========================================  
+ Coverage   93.67%   93.74%   +0.07%       
==========================================  
  Files          93       94       +1       
  Lines        7213     7150      -63       
==========================================  
- Hits         6757     6703      -54       
+ Misses        456      447       -9  
```  
  
  
| [Impacted Files](https://codecov.io/gh/vinniefalco/Beast/pull/541?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [include/beast/http/dynamic\_body.hpp](https://codecov.io/gh/vinniefalco/Beast/pull/541?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9odHRwL2R5bmFtaWNfYm9keS5ocHA=) | `85.71% <ø> (ø)` | :arrow_up: |  
| [include/beast/http/empty\_body.hpp](https://codecov.io/gh/vinniefalco/Beast/pull/541?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9odHRwL2VtcHR5X2JvZHkuaHBw) | `71.42% <ø> (+14.28%)` | :arrow_up: |  
| [include/beast/http/impl/basic\_parser.ipp](https://codecov.io/gh/vinniefalco/Beast/pull/541?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9odHRwL2ltcGwvYmFzaWNfcGFyc2VyLmlwcA==) | `95.4% <ø> (-0.06%)` | :arrow_down: |  
| [include/beast/http/string\_view\_body.hpp](https://codecov.io/gh/vinniefalco/Beast/pull/541?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9odHRwL3N0cmluZ192aWV3X2JvZHkuaHBw) | `100% <ø> (ø)` | :arrow_up: |  
| [include/beast/http/buffer\_body.hpp](https://codecov.io/gh/vinniefalco/Beast/pull/541?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9odHRwL2J1ZmZlcl9ib2R5LmhwcA==) | `96.29% <ø> (+3.43%)` | :arrow_up: |  
| [include/beast/http/impl/field.ipp](https://codecov.io/gh/vinniefalco/Beast/pull/541?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9odHRwL2ltcGwvZmllbGQuaXBw) | `100% <100%> (ø)` | :arrow_up: |  
| [include/beast/http/impl/error.ipp](https://codecov.io/gh/vinniefalco/Beast/pull/541?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9odHRwL2ltcGwvZXJyb3IuaXBw) | `97.29% <100%> (ø)` | :arrow_up: |  
| [include/beast/http/serializer.hpp](https://codecov.io/gh/vinniefalco/Beast/pull/541?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9odHRwL3NlcmlhbGl6ZXIuaHBw) | `100% <100%> (ø)` | :arrow_up: |  
| [include/beast/http/fields.hpp](https://codecov.io/gh/vinniefalco/Beast/pull/541?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9odHRwL2ZpZWxkcy5ocHA=) | `100% <100%> (ø)` | :arrow_up: |  
| [include/beast/core/string.hpp](https://codecov.io/gh/vinniefalco/Beast/pull/541?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9jb3JlL3N0cmluZy5ocHA=) | `100% <100%> (ø)` | :arrow_up: |  
| ... and [17 more](https://codecov.io/gh/vinniefalco/Beast/pull/541?src=pr&el=tree-more) | |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/vinniefalco/Beast/pull/541?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/vinniefalco/Beast/pull/541?src=pr&el=footer). Last update [8982e14...4269f35](https://codecov.io/gh/vinniefalco/Beast/pull/541?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---
