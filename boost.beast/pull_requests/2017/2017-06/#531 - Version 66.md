# #531 Version 66 [Merged]

> Username: vinniefalco  
> Created at: 2017-06-23 02:14:23 UTC  
> Updated at: 2017-06-24 01:24:25 UTC  
> Merged at: 2017-06-23 23:13:51 UTC  
> Closed at: 2017-06-23 23:13:51 UTC  
> Url: https://github.com/boostorg/beast/pull/531  

* string_param optimizations  
* Add serializer request/response aliases  
* Make consuming_buffers smaller  
* Fix costly potential value-init in parser  
* Fix unused parameter warning  
* Handle bad_alloc in parser  
* Tidy up message piecewise ctors  
* Add header aliases  
* basic_fields optimizations  
* Squelch spurious warning on gcc  
  
Documentation preview:  
http://vinniefalco.github.io/stage/beast/v66

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2017-06-23 03:21:34 UTC  
> Updated_at: 2017-06-23 23:48:43 UTC  
> Url: https://github.com/boostorg/beast/pull/531#issuecomment-310560729  

# [Codecov](https://codecov.io/gh/vinniefalco/Beast/pull/531?src=pr&el=h1) Report  
> Merging [#531](https://codecov.io/gh/vinniefalco/Beast/pull/531?src=pr&el=desc) into [master](https://codecov.io/gh/vinniefalco/Beast/commit/c67525281f089d1160d9e43f86ed6d9b34f2fc31?src=pr&el=desc) will **increase** coverage by `0.25%`.  
> The diff coverage is `91.94%`.  
  
[![Impacted file tree graph](https://codecov.io/gh/vinniefalco/Beast/pull/531/graphs/tree.svg?height=150&src=pr&token=Gq6MFA9JRF&width=650)](https://codecov.io/gh/vinniefalco/Beast/pull/531?src=pr&el=tree)  
  
```diff  
@@            Coverage Diff             @@  
##           master     #531      +/-   ##  
==========================================  
+ Coverage   93.47%   93.73%   +0.25%       
==========================================  
  Files          95       96       +1       
  Lines        7077     7114      +37       
==========================================  
+ Hits         6615     6668      +53       
+ Misses        462      446      -16  
```  
  
  
| [Impacted Files](https://codecov.io/gh/vinniefalco/Beast/pull/531?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [include/beast/core/string\_param.hpp](https://codecov.io/gh/vinniefalco/Beast/pull/531?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9jb3JlL3N0cmluZ19wYXJhbS5ocHA=) | `100% <ø> (ø)` | :arrow_up: |  
| [include/beast/http/message.hpp](https://codecov.io/gh/vinniefalco/Beast/pull/531?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9odHRwL21lc3NhZ2UuaHBw) | `100% <ø> (ø)` | :arrow_up: |  
| [include/beast/http/fields.hpp](https://codecov.io/gh/vinniefalco/Beast/pull/531?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9odHRwL2ZpZWxkcy5ocHA=) | `100% <ø> (ø)` | :arrow_up: |  
| [include/beast/core/impl/consuming\_buffers.ipp](https://codecov.io/gh/vinniefalco/Beast/pull/531?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9jb3JlL2ltcGwvY29uc3VtaW5nX2J1ZmZlcnMuaXBw) | `95% <ø> (ø)` | :arrow_up: |  
| [include/beast/core/consuming\_buffers.hpp](https://codecov.io/gh/vinniefalco/Beast/pull/531?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9jb3JlL2NvbnN1bWluZ19idWZmZXJzLmhwcA==) | `100% <ø> (ø)` | :arrow_up: |  
| [include/beast/http/serializer.hpp](https://codecov.io/gh/vinniefalco/Beast/pull/531?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9odHRwL3NlcmlhbGl6ZXIuaHBw) | `100% <ø> (ø)` | :arrow_up: |  
| [include/beast/core/impl/static\_string.ipp](https://codecov.io/gh/vinniefalco/Beast/pull/531?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9jb3JlL2ltcGwvc3RhdGljX3N0cmluZy5pcHA=) | `99.42% <100%> (-0.07%)` | :arrow_down: |  
| [include/beast/core/impl/string\_param.ipp](https://codecov.io/gh/vinniefalco/Beast/pull/531?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9jb3JlL2ltcGwvc3RyaW5nX3BhcmFtLmlwcA==) | `100% <100%> (ø)` | |  
| [include/beast/core/detail/static\_string.hpp](https://codecov.io/gh/vinniefalco/Beast/pull/531?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9jb3JlL2RldGFpbC9zdGF0aWNfc3RyaW5nLmhwcA==) | `100% <100%> (ø)` | :arrow_up: |  
| [include/beast/zlib/detail/deflate\_stream.hpp](https://codecov.io/gh/vinniefalco/Beast/pull/531?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC96bGliL2RldGFpbC9kZWZsYXRlX3N0cmVhbS5ocHA=) | `92.83% <100%> (+0.01%)` | :arrow_up: |  
| ... and [11 more](https://codecov.io/gh/vinniefalco/Beast/pull/531?src=pr&el=tree-more) | |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/vinniefalco/Beast/pull/531?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/vinniefalco/Beast/pull/531?src=pr&el=footer). Last update [c675252...7fb75d0](https://codecov.io/gh/vinniefalco/Beast/pull/531?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---
