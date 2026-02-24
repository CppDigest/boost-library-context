# #913 Version 147 [Merged]

> Username: vinniefalco  
> Created at: 2017-11-28 00:49:47 UTC  
> Updated at: 2017-12-02 21:18:59 UTC  
> Merged at: 2017-11-29 04:09:47 UTC  
> Closed at: 2017-11-29 04:09:47 UTC  
> Url: https://github.com/boostorg/beast/pull/913  

* Don't use boost::string_ref  
* Use iterator wrapper in detail::buffers_range  
  
HTTP:  
  
* Tidy up basic_fields exception specifiers  
  
WebSocket:  
  
* control callback is copied or moved  
* Send idle pings in advanced servers

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2017-11-28 08:49:20 UTC  
> Updated_at: 2017-11-28 16:15:26 UTC  
> Url: https://github.com/boostorg/beast/pull/913#issuecomment-347453966  

# [Codecov](https://codecov.io/gh/boostorg/beast/pull/913?src=pr&el=h1) Report  
> Merging [#913](https://codecov.io/gh/boostorg/beast/pull/913?src=pr&el=desc) into [develop](https://codecov.io/gh/boostorg/beast/commit/5d83c79be408c8a29dd7eec413e9dc699648cdba?src=pr&el=desc) will **increase** coverage by `<.01%`.  
> The diff coverage is `100%`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/beast/pull/913/graphs/tree.svg?src=pr&token=Gq6MFA9JRF&width=650&height=150)](https://codecov.io/gh/boostorg/beast/pull/913?src=pr&el=tree)  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #913      +/-   ##  
===========================================  
+ Coverage    95.68%   95.68%   +<.01%       
===========================================  
  Files          104      104                
  Lines        10441    10454      +13       
===========================================  
+ Hits          9990    10003      +13       
  Misses         451      451  
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/beast/pull/913?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [include/boost/beast/http/impl/fields.ipp](https://codecov.io/gh/boostorg/beast/pull/913?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9odHRwL2ltcGwvZmllbGRzLmlwcA==) | `97.72% <ø> (ø)` | :arrow_up: |  
| [include/boost/beast/http/fields.hpp](https://codecov.io/gh/boostorg/beast/pull/913?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9odHRwL2ZpZWxkcy5ocHA=) | `100% <ø> (ø)` | :arrow_up: |  
| [include/boost/beast/core/string.hpp](https://codecov.io/gh/boostorg/beast/pull/913?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9jb3JlL3N0cmluZy5ocHA=) | `100% <ø> (ø)` | :arrow_up: |  
| [include/boost/beast/core/detail/type\_traits.hpp](https://codecov.io/gh/boostorg/beast/pull/913?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9jb3JlL2RldGFpbC90eXBlX3RyYWl0cy5ocHA=) | `100% <100%> (ø)` | :arrow_up: |  
| [include/boost/beast/websocket/stream.hpp](https://codecov.io/gh/boostorg/beast/pull/913?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC93ZWJzb2NrZXQvc3RyZWFtLmhwcA==) | `100% <100%> (ø)` | :arrow_up: |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/beast/pull/913?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/beast/pull/913?src=pr&el=footer). Last update [5d83c79...bfd4378](https://codecov.io/gh/boostorg/beast/pull/913?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---
