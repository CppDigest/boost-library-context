# #649 Version 81 [Merged]

> Username: vinniefalco  
> Created at: 2017-07-14 01:12:49 UTC  
> Updated at: 2017-07-15 04:39:11 UTC  
> Merged at: 2017-07-15 04:16:20 UTC  
> Closed at: 2017-07-15 04:16:20 UTC  
> Url: https://github.com/boostorg/beast/pull/649  

**NOTE** Version 80 is in a separate pull request  
  
* Pass string_view by value  
* better is_final on empty_base_optimization  
* Suppress incorrect GCC warning  
* multi_buffer ctor is explicit  
* File is not copy-constructible  
  
API Changes:  
  
* Refactor basic_parser, chunk parsing:  
  
Actions Required:  
  
* Adjust signatures for required members of user-defined  
  subclasses of basic_parser  
  
* Use the new basic_parser chunk callbacks for accessing  
  chunk extensions and chunk bodies.  
  
Documentation preview:  
http://vinniefalco.github.io/stage/beast/v81

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2017-07-14 01:56:47 UTC  
> Updated_at: 2017-07-15 04:39:11 UTC  
> Url: https://github.com/boostorg/beast/pull/649#issuecomment-315248372  

# [Codecov](https://codecov.io/gh/vinniefalco/Beast/pull/649?src=pr&el=h1) Report  
> Merging [#649](https://codecov.io/gh/vinniefalco/Beast/pull/649?src=pr&el=desc) into [develop](https://codecov.io/gh/vinniefalco/Beast/commit/3bcd9865f80f12ba5faad35c564918f85b02e271?src=pr&el=desc) will **increase** coverage by `0.01%`.  
> The diff coverage is `95.37%`.  
  
[![Impacted file tree graph](https://codecov.io/gh/vinniefalco/Beast/pull/649/graphs/tree.svg?src=pr&token=Gq6MFA9JRF&width=650&height=150)](https://codecov.io/gh/vinniefalco/Beast/pull/649?src=pr&el=tree)  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #649      +/-   ##  
===========================================  
+ Coverage    93.86%   93.88%   +0.01%       
===========================================  
  Files          102      106       +4       
  Lines         7764     8099     +335       
===========================================  
+ Hits          7288     7604     +316       
- Misses         476      495      +19  
```  
  
  
| [Impacted Files](https://codecov.io/gh/vinniefalco/Beast/pull/649?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [include/beast/core/multi\_buffer.hpp](https://codecov.io/gh/vinniefalco/Beast/pull/649?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9jb3JlL211bHRpX2J1ZmZlci5ocHA=) | `100% <ø> (ø)` | :arrow_up: |  
| [include/beast/core/impl/string\_param.ipp](https://codecov.io/gh/vinniefalco/Beast/pull/649?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9jb3JlL2ltcGwvc3RyaW5nX3BhcmFtLmlwcA==) | `100% <ø> (ø)` | :arrow_up: |  
| [include/beast/core/string\_param.hpp](https://codecov.io/gh/vinniefalco/Beast/pull/649?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9jb3JlL3N0cmluZ19wYXJhbS5ocHA=) | `100% <ø> (ø)` | :arrow_up: |  
| [include/beast/http/basic\_parser.hpp](https://codecov.io/gh/vinniefalco/Beast/pull/649?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9odHRwL2Jhc2ljX3BhcnNlci5ocHA=) | `100% <ø> (ø)` | :arrow_up: |  
| [include/beast/core/flat\_buffer.hpp](https://codecov.io/gh/vinniefalco/Beast/pull/649?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9jb3JlL2ZsYXRfYnVmZmVyLmhwcA==) | `100% <ø> (ø)` | :arrow_up: |  
| [include/beast/core/drain\_buffer.hpp](https://codecov.io/gh/vinniefalco/Beast/pull/649?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9jb3JlL2RyYWluX2J1ZmZlci5ocHA=) | `100% <ø> (ø)` | :arrow_up: |  
| [...lude/beast/core/detail/empty\_base\_optimization.hpp](https://codecov.io/gh/vinniefalco/Beast/pull/649?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9jb3JlL2RldGFpbC9lbXB0eV9iYXNlX29wdGltaXphdGlvbi5ocHA=) | `100% <ø> (ø)` | :arrow_up: |  
| [include/beast/core/string.hpp](https://codecov.io/gh/vinniefalco/Beast/pull/649?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9jb3JlL3N0cmluZy5ocHA=) | `100% <ø> (ø)` | :arrow_up: |  
| [include/beast/http/fields.hpp](https://codecov.io/gh/vinniefalco/Beast/pull/649?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9odHRwL2ZpZWxkcy5ocHA=) | `100% <ø> (ø)` | :arrow_up: |  
| [include/beast/http/serializer.hpp](https://codecov.io/gh/vinniefalco/Beast/pull/649?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9odHRwL3NlcmlhbGl6ZXIuaHBw) | `100% <ø> (ø)` | :arrow_up: |  
| ... and [39 more](https://codecov.io/gh/vinniefalco/Beast/pull/649?src=pr&el=tree-more) | |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/vinniefalco/Beast/pull/649?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/vinniefalco/Beast/pull/649?src=pr&el=footer). Last update [3bcd986...fde46eb](https://codecov.io/gh/vinniefalco/Beast/pull/649?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---
