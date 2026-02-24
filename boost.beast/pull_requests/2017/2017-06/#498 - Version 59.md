# #498 Version 59 [Merged]

> Username: vinniefalco  
> Created at: 2017-06-16 02:34:10 UTC  
> Updated at: 2017-06-16 17:07:01 UTC  
> Merged at: 2017-06-16 16:19:19 UTC  
> Closed at: 2017-06-16 16:19:19 UTC  
> Url: https://github.com/boostorg/beast/pull/498  

Version 59:  
  
* Integrated Beast INTERFACE (cmake)  
* Fix base64 alphabet  
  
API Changes:  
  
* Change Body::size signature (API Change):  
  
Actions Required:  
  
* For any user-defined models of Body, change the function signature to accept `value_type const&` and modify the function definition accordingly.  
  
Documentation preview:  
http://vinniefalco.github.io/stage/beast/v59/

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2017-06-16 04:14:02 UTC  
> Updated_at: 2017-06-16 17:07:01 UTC  
> Url: https://github.com/boostorg/beast/pull/498#issuecomment-308928521  

# [Codecov](https://codecov.io/gh/vinniefalco/Beast/pull/498?src=pr&el=h1) Report  
> Merging [#498](https://codecov.io/gh/vinniefalco/Beast/pull/498?src=pr&el=desc) into [master](https://codecov.io/gh/vinniefalco/Beast/commit/3f8097b6fddce8463084ce5c0d69db9a9079c2b8?src=pr&el=desc) will **not change** coverage.  
> The diff coverage is `100%`.  
  
[![Impacted file tree graph](https://codecov.io/gh/vinniefalco/Beast/pull/498/graphs/tree.svg?width=650&height=150&src=pr&token=Gq6MFA9JRF)](https://codecov.io/gh/vinniefalco/Beast/pull/498?src=pr&el=tree)  
  
```diff  
@@           Coverage Diff           @@  
##           master     #498   +/-   ##  
=======================================  
  Coverage   93.51%   93.51%             
=======================================  
  Files          96       96             
  Lines        7093     7093             
=======================================  
  Hits         6633     6633             
  Misses        460      460  
```  
  
  
| [Impacted Files](https://codecov.io/gh/vinniefalco/Beast/pull/498?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [include/beast/http/empty\_body.hpp](https://codecov.io/gh/vinniefalco/Beast/pull/498?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9odHRwL2VtcHR5X2JvZHkuaHBw) | `50% <ø> (ø)` | :arrow_up: |  
| [include/beast/core/detail/base64.hpp](https://codecov.io/gh/vinniefalco/Beast/pull/498?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9jb3JlL2RldGFpbC9iYXNlNjQuaHBw) | `100% <ø> (ø)` | :arrow_up: |  
| [include/beast/http/dynamic\_body.hpp](https://codecov.io/gh/vinniefalco/Beast/pull/498?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9odHRwL2R5bmFtaWNfYm9keS5ocHA=) | `85.71% <ø> (ø)` | :arrow_up: |  
| [include/beast/http/impl/message.ipp](https://codecov.io/gh/vinniefalco/Beast/pull/498?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9odHRwL2ltcGwvbWVzc2FnZS5pcHA=) | `89.47% <100%> (ø)` | :arrow_up: |  
| [include/beast/http/string\_view\_body.hpp](https://codecov.io/gh/vinniefalco/Beast/pull/498?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9odHRwL3N0cmluZ192aWV3X2JvZHkuaHBw) | `100% <100%> (ø)` | :arrow_up: |  
| [include/beast/http/string\_body.hpp](https://codecov.io/gh/vinniefalco/Beast/pull/498?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9odHRwL3N0cmluZ19ib2R5LmhwcA==) | `91.3% <100%> (ø)` | :arrow_up: |  
| [include/beast/http/message.hpp](https://codecov.io/gh/vinniefalco/Beast/pull/498?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9odHRwL21lc3NhZ2UuaHBw) | `100% <100%> (ø)` | :arrow_up: |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/vinniefalco/Beast/pull/498?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/vinniefalco/Beast/pull/498?src=pr&el=footer). Last update [3f8097b...4e4bcf8](https://codecov.io/gh/vinniefalco/Beast/pull/498?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---
