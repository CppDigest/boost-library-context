# #420 Version 48 [Merged]

> Username: vinniefalco  
> Created at: 2017-06-04 13:52:34 UTC  
> Updated at: 2017-06-08 01:19:41 UTC  
> Merged at: 2017-06-05 23:07:08 UTC  
> Closed at: 2017-06-05 23:07:08 UTC  
> Url: https://github.com/boostorg/beast/pull/420  

* Make buffer_prefix_view public  
* Remove detail::sync_ostream  
* Tidy up core type traits  
  
API Changes:  
  
* Tidy up chunk decorator  
* Rename to buffer_cat_view  
* Consolidate parsers to parser.hpp  
* Rename to parser  
  
Documentation preview:  
http://vinniefalco.github.io/stage/beast/v48/

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2017-06-04 14:22:12 UTC  
> Updated_at: 2017-06-04 14:37:59 UTC  
> Url: https://github.com/boostorg/beast/pull/420#issuecomment-306043009  

# [Codecov](https://codecov.io/gh/vinniefalco/Beast/pull/420?src=pr&el=h1) Report  
> Merging [#420](https://codecov.io/gh/vinniefalco/Beast/pull/420?src=pr&el=desc) into [master](https://codecov.io/gh/vinniefalco/Beast/commit/458fa7e4e239e1c9e41066e61da2ae8f71d26220?src=pr&el=desc) will **decrease** coverage by `0.02%`.  
> The diff coverage is `91.35%`.  
  
[![Impacted file tree graph](https://codecov.io/gh/vinniefalco/Beast/pull/420/graphs/tree.svg?token=Gq6MFA9JRF&width=650&height=150&src=pr)](https://codecov.io/gh/vinniefalco/Beast/pull/420?src=pr&el=tree)  
  
```diff  
@@            Coverage Diff             @@  
##           master     #420      +/-   ##  
==========================================  
- Coverage    94.1%   94.07%   -0.03%       
==========================================  
  Files          94       92       -2       
  Lines        6678     6685       +7       
==========================================  
+ Hits         6284     6289       +5       
- Misses        394      396       +2  
```  
  
  
| [Impacted Files](https://codecov.io/gh/vinniefalco/Beast/pull/420?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [include/beast/websocket/stream.hpp](https://codecov.io/gh/vinniefalco/Beast/pull/420?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC93ZWJzb2NrZXQvc3RyZWFtLmhwcA==) | `100% <ø> (ø)` | :arrow_up: |  
| [include/beast/websocket/impl/accept.ipp](https://codecov.io/gh/vinniefalco/Beast/pull/420?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC93ZWJzb2NrZXQvaW1wbC9hY2NlcHQuaXBw) | `100% <ø> (ø)` | :arrow_up: |  
| [include/beast/http/detail/chunk\_encode.hpp](https://codecov.io/gh/vinniefalco/Beast/pull/420?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9odHRwL2RldGFpbC9jaHVua19lbmNvZGUuaHBw) | `95.65% <ø> (ø)` | :arrow_up: |  
| [include/beast/http/basic\_parser.hpp](https://codecov.io/gh/vinniefalco/Beast/pull/420?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9odHRwL2Jhc2ljX3BhcnNlci5ocHA=) | `100% <ø> (ø)` | :arrow_up: |  
| [include/beast/core/static\_string.hpp](https://codecov.io/gh/vinniefalco/Beast/pull/420?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9jb3JlL3N0YXRpY19zdHJpbmcuaHBw) | `100% <ø> (ø)` | :arrow_up: |  
| [include/beast/http/impl/serializer.ipp](https://codecov.io/gh/vinniefalco/Beast/pull/420?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9odHRwL2ltcGwvc2VyaWFsaXplci5pcHA=) | `96.4% <ø> (+0.02%)` | :arrow_up: |  
| [include/beast/core/async\_result.hpp](https://codecov.io/gh/vinniefalco/Beast/pull/420?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9jb3JlL2FzeW5jX3Jlc3VsdC5ocHA=) | `100% <ø> (ø)` | :arrow_up: |  
| [include/beast/core/flat\_buffer.hpp](https://codecov.io/gh/vinniefalco/Beast/pull/420?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9jb3JlL2ZsYXRfYnVmZmVyLmhwcA==) | `100% <ø> (ø)` | :arrow_up: |  
| [include/beast/http/impl/parser.ipp](https://codecov.io/gh/vinniefalco/Beast/pull/420?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9odHRwL2ltcGwvcGFyc2VyLmlwcA==) | `100% <100%> (ø)` | |  
| [include/beast/core/buffer\_prefix.hpp](https://codecov.io/gh/vinniefalco/Beast/pull/420?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9jb3JlL2J1ZmZlcl9wcmVmaXguaHBw) | `100% <100%> (ø)` | :arrow_up: |  
| ... and [11 more](https://codecov.io/gh/vinniefalco/Beast/pull/420?src=pr&el=tree-more) | |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/vinniefalco/Beast/pull/420?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/vinniefalco/Beast/pull/420?src=pr&el=footer). Last update [458fa7e...d77e423](https://codecov.io/gh/vinniefalco/Beast/pull/420?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---
