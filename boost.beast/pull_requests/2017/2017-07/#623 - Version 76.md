# #623 Version 76 [Merged]

> Username: vinniefalco  
> Created at: 2017-07-08 15:01:36 UTC  
> Updated at: 2017-07-09 03:34:37 UTC  
> Merged at: 2017-07-09 01:53:43 UTC  
> Closed at: 2017-07-09 01:53:43 UTC  
> Url: https://github.com/boostorg/beast/pull/623  

* Always go through write_some  
* Use Boost.Config  
* BodyReader may construct from a non-const message  
* Add serializer::get  
* Add serializer::chunked  
* Serializer members are not const  
* serializing file_body is not const  
* Add file_body_win32  
* Fix parse illegal characters in obs-fold  
  
API Changes:  
  
* Rename to serializer::keep_alive  
* BodyReader, BodyWriter use two-phase init  
  
Actions Required:  
  
* Use serializer::keep_alive instead of serializer::close and  
  take the logical NOT of the return value.  
  
* Modify instances of user-defined BodyReader and BodyWriter  
  types to perfrom two-phase initialization, as per the  
  updated documented type requirements.  
  
Documentation preview:  
http://vinniefalco.github.io/stage/beast/v76

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2017-07-08 18:04:56 UTC  
> Url: https://github.com/boostorg/beast/pull/623#issuecomment-313871812  

# [Codecov](https://codecov.io/gh/vinniefalco/Beast/pull/623?src=pr&el=h1) Report  
> Merging [#623](https://codecov.io/gh/vinniefalco/Beast/pull/623?src=pr&el=desc) into [develop](https://codecov.io/gh/vinniefalco/Beast/commit/0e2306603b6fb57efa74265c27f8ae7f8762a2e5?src=pr&el=desc) will **increase** coverage by `0.1%`.  
> The diff coverage is `94.38%`.  
  
[![Impacted file tree graph](https://codecov.io/gh/vinniefalco/Beast/pull/623/graphs/tree.svg?height=150&width=650&src=pr&token=Gq6MFA9JRF)](https://codecov.io/gh/vinniefalco/Beast/pull/623?src=pr&el=tree)  
  
```diff  
@@            Coverage Diff             @@  
##           develop     #623     +/-   ##  
==========================================  
+ Coverage    93.55%   93.66%   +0.1%       
==========================================  
  Files          102      102               
  Lines         7738     7713     -25       
==========================================  
- Hits          7239     7224     -15       
+ Misses         499      489     -10  
```  
  
  
| [Impacted Files](https://codecov.io/gh/vinniefalco/Beast/pull/623?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [include/beast/http/string\_view\_body.hpp](https://codecov.io/gh/vinniefalco/Beast/pull/623?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9odHRwL3N0cmluZ192aWV3X2JvZHkuaHBw) | `100% <ø> (ø)` | :arrow_up: |  
| [include/beast/core/detail/cpu\_info.hpp](https://codecov.io/gh/vinniefalco/Beast/pull/623?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9jb3JlL2RldGFpbC9jcHVfaW5mby5ocHA=) | `100% <ø> (ø)` | :arrow_up: |  
| [include/beast/http/empty\_body.hpp](https://codecov.io/gh/vinniefalco/Beast/pull/623?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9odHRwL2VtcHR5X2JvZHkuaHBw) | `71.42% <ø> (+14.28%)` | :arrow_up: |  
| [include/beast/http/impl/parser.ipp](https://codecov.io/gh/vinniefalco/Beast/pull/623?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9odHRwL2ltcGwvcGFyc2VyLmlwcA==) | `100% <100%> (+11.11%)` | :arrow_up: |  
| [include/beast/http/dynamic\_body.hpp](https://codecov.io/gh/vinniefalco/Beast/pull/623?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9odHRwL2R5bmFtaWNfYm9keS5ocHA=) | `82.6% <100%> (ø)` | :arrow_up: |  
| [include/beast/http/buffer\_body.hpp](https://codecov.io/gh/vinniefalco/Beast/pull/623?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9odHRwL2J1ZmZlcl9ib2R5LmhwcA==) | `90.62% <100%> (ø)` | :arrow_up: |  
| [include/beast/http/string\_body.hpp](https://codecov.io/gh/vinniefalco/Beast/pull/623?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9odHRwL3N0cmluZ19ib2R5LmhwcA==) | `85.71% <100%> (+0.52%)` | :arrow_up: |  
| [include/beast/http/file\_body.hpp](https://codecov.io/gh/vinniefalco/Beast/pull/623?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9odHRwL2ZpbGVfYm9keS5ocHA=) | `82.5% <100%> (+0.44%)` | :arrow_up: |  
| [include/beast/http/parser.hpp](https://codecov.io/gh/vinniefalco/Beast/pull/623?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9odHRwL3BhcnNlci5ocHA=) | `77.77% <100%> (-2.23%)` | :arrow_down: |  
| [include/beast/http/serializer.hpp](https://codecov.io/gh/vinniefalco/Beast/pull/623?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9odHRwL3NlcmlhbGl6ZXIuaHBw) | `100% <100%> (ø)` | :arrow_up: |  
| ... and [4 more](https://codecov.io/gh/vinniefalco/Beast/pull/623?src=pr&el=tree-more) | |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/vinniefalco/Beast/pull/623?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/vinniefalco/Beast/pull/623?src=pr&el=footer). Last update [0e23066...611432a](https://codecov.io/gh/vinniefalco/Beast/pull/623?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---

## Comment 2

> Username: codecov[bot]  
> Created_at: 2017-07-08 18:04:56 UTC  
> Updated_at: 2017-07-09 03:01:54 UTC  
> Url: https://github.com/boostorg/beast/pull/623#issuecomment-313871813  

# [Codecov](https://codecov.io/gh/vinniefalco/Beast/pull/623?src=pr&el=h1) Report  
> Merging [#623](https://codecov.io/gh/vinniefalco/Beast/pull/623?src=pr&el=desc) into [develop](https://codecov.io/gh/vinniefalco/Beast/commit/0e2306603b6fb57efa74265c27f8ae7f8762a2e5?src=pr&el=desc) will **increase** coverage by `0.12%`.  
> The diff coverage is `94.38%`.  
  
[![Impacted file tree graph](https://codecov.io/gh/vinniefalco/Beast/pull/623/graphs/tree.svg?height=150&width=650&token=Gq6MFA9JRF&src=pr)](https://codecov.io/gh/vinniefalco/Beast/pull/623?src=pr&el=tree)  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #623      +/-   ##  
===========================================  
+ Coverage    93.55%   93.67%   +0.12%       
===========================================  
  Files          102      101       -1       
  Lines         7738     7681      -57       
===========================================  
- Hits          7239     7195      -44       
+ Misses         499      486      -13  
```  
  
  
| [Impacted Files](https://codecov.io/gh/vinniefalco/Beast/pull/623?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [include/beast/http/string\_view\_body.hpp](https://codecov.io/gh/vinniefalco/Beast/pull/623?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9odHRwL3N0cmluZ192aWV3X2JvZHkuaHBw) | `100% <ø> (ø)` | :arrow_up: |  
| [include/beast/http/empty\_body.hpp](https://codecov.io/gh/vinniefalco/Beast/pull/623?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9odHRwL2VtcHR5X2JvZHkuaHBw) | `71.42% <ø> (+14.28%)` | :arrow_up: |  
| [include/beast/http/string\_body.hpp](https://codecov.io/gh/vinniefalco/Beast/pull/623?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9odHRwL3N0cmluZ19ib2R5LmhwcA==) | `85.71% <100%> (+0.52%)` | :arrow_up: |  
| [include/beast/http/impl/parser.ipp](https://codecov.io/gh/vinniefalco/Beast/pull/623?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9odHRwL2ltcGwvcGFyc2VyLmlwcA==) | `100% <100%> (+11.11%)` | :arrow_up: |  
| [include/beast/http/dynamic\_body.hpp](https://codecov.io/gh/vinniefalco/Beast/pull/623?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9odHRwL2R5bmFtaWNfYm9keS5ocHA=) | `82.6% <100%> (ø)` | :arrow_up: |  
| [include/beast/http/serializer.hpp](https://codecov.io/gh/vinniefalco/Beast/pull/623?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9odHRwL3NlcmlhbGl6ZXIuaHBw) | `100% <100%> (ø)` | :arrow_up: |  
| [include/beast/http/file\_body.hpp](https://codecov.io/gh/vinniefalco/Beast/pull/623?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9odHRwL2ZpbGVfYm9keS5ocHA=) | `82.5% <100%> (+0.44%)` | :arrow_up: |  
| [include/beast/http/impl/serializer.ipp](https://codecov.io/gh/vinniefalco/Beast/pull/623?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9odHRwL2ltcGwvc2VyaWFsaXplci5pcHA=) | `98.06% <100%> (-0.03%)` | :arrow_down: |  
| [include/beast/http/detail/basic\_parser.hpp](https://codecov.io/gh/vinniefalco/Beast/pull/623?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9odHRwL2RldGFpbC9iYXNpY19wYXJzZXIuaHBw) | `97.54% <100%> (-0.21%)` | :arrow_down: |  
| [include/beast/http/parser.hpp](https://codecov.io/gh/vinniefalco/Beast/pull/623?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9odHRwL3BhcnNlci5ocHA=) | `77.77% <100%> (-2.23%)` | :arrow_down: |  
| ... and [6 more](https://codecov.io/gh/vinniefalco/Beast/pull/623?src=pr&el=tree-more) | |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/vinniefalco/Beast/pull/623?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/vinniefalco/Beast/pull/623?src=pr&el=footer). Last update [0e23066...1bc30cb](https://codecov.io/gh/vinniefalco/Beast/pull/623?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---
