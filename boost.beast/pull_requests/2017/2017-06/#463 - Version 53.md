# #463 Version 53 [Closed]

> Username: vinniefalco  
> Created at: 2017-06-10 17:17:22 UTC  
> Updated at: 2017-06-11 02:50:18 UTC  
> Closed at: 2017-06-11 02:46:58 UTC  
> Url: https://github.com/boostorg/beast/pull/463  

* Fix basic_parser::maybe_flatten

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2017-06-10 17:17:25 UTC  
> Updated_at: 2017-06-10 20:53:51 UTC  
> Url: https://github.com/boostorg/beast/pull/463#issuecomment-307578480  

# [Codecov](https://codecov.io/gh/vinniefalco/Beast/pull/463?src=pr&el=h1) Report  
> Merging [#463](https://codecov.io/gh/vinniefalco/Beast/pull/463?src=pr&el=desc) into [master](https://codecov.io/gh/vinniefalco/Beast/commit/76402f7d9206a352d6fcaaef615c95c093733560?src=pr&el=desc) will **decrease** coverage by `0.03%`.  
> The diff coverage is `90%`.  
  
[![Impacted file tree graph](https://codecov.io/gh/vinniefalco/Beast/pull/463/graphs/tree.svg?width=650&height=150&src=pr&token=Gq6MFA9JRF)](https://codecov.io/gh/vinniefalco/Beast/pull/463?src=pr&el=tree)  
  
```diff  
@@            Coverage Diff             @@  
##           master     #463      +/-   ##  
==========================================  
- Coverage   93.23%   93.19%   -0.04%       
==========================================  
  Files          92       92                
  Lines        6736     6741       +5       
==========================================  
+ Hits         6280     6282       +2       
- Misses        456      459       +3  
```  
  
  
| [Impacted Files](https://codecov.io/gh/vinniefalco/Beast/pull/463?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [include/beast/http/impl/read.ipp](https://codecov.io/gh/vinniefalco/Beast/pull/463?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9odHRwL2ltcGwvcmVhZC5pcHA=) | `89.87% <100%> (ø)` | :arrow_up: |  
| [include/beast/core/detail/ostream.hpp](https://codecov.io/gh/vinniefalco/Beast/pull/463?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9jb3JlL2RldGFpbC9vc3RyZWFtLmhwcA==) | `93.02% <100%> (ø)` | :arrow_up: |  
| [include/beast/http/impl/basic\_parser.ipp](https://codecov.io/gh/vinniefalco/Beast/pull/463?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9odHRwL2ltcGwvYmFzaWNfcGFyc2VyLmlwcA==) | `98.96% <100%> (ø)` | :arrow_up: |  
| [include/beast/websocket/detail/pmd\_extension.hpp](https://codecov.io/gh/vinniefalco/Beast/pull/463?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC93ZWJzb2NrZXQvZGV0YWlsL3BtZF9leHRlbnNpb24uaHBw) | `81.81% <100%> (ø)` | :arrow_up: |  
| [include/beast/core/detail/read\_size\_helper.hpp](https://codecov.io/gh/vinniefalco/Beast/pull/463?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9jb3JlL2RldGFpbC9yZWFkX3NpemVfaGVscGVyLmhwcA==) | `84.61% <83.33%> (-2.89%)` | :arrow_down: |  
| [include/beast/core/impl/flat\_buffer.ipp](https://codecov.io/gh/vinniefalco/Beast/pull/463?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9jb3JlL2ltcGwvZmxhdF9idWZmZXIuaXBw) | `87.5% <0%> (-1.57%)` | :arrow_down: |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/vinniefalco/Beast/pull/463?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/vinniefalco/Beast/pull/463?src=pr&el=footer). Last update [76402f7...4dd77cd](https://codecov.io/gh/vinniefalco/Beast/pull/463?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---

## Comment 2

> Username: vinniefalco  
> Created_at: 2017-06-11 02:46:58 UTC  
> Url: https://github.com/boostorg/beast/pull/463#issuecomment-307602516  

Merged

---
