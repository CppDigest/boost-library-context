# #502 Version 60 [Closed]

> Username: vinniefalco  
> Created at: 2017-06-16 17:11:29 UTC  
> Updated at: 2017-06-17 19:03:44 UTC  
> Closed at: 2017-06-17 02:33:08 UTC  
> Url: https://github.com/boostorg/beast/pull/502  

* String comparisons are public interfaces  
* Fix response message type in async websocket accept  
* New server-framework, full featured server example  
  
Documentation preview:  
http://vinniefalco.github.io/stage/beast/v60

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2017-06-16 17:40:30 UTC  
> Updated_at: 2017-06-17 01:26:47 UTC  
> Url: https://github.com/boostorg/beast/pull/502#issuecomment-309088888  

# [Codecov](https://codecov.io/gh/vinniefalco/Beast/pull/502?src=pr&el=h1) Report  
> Merging [#502](https://codecov.io/gh/vinniefalco/Beast/pull/502?src=pr&el=desc) into [master](https://codecov.io/gh/vinniefalco/Beast/commit/4e4bcf8b1114229713eec0cf5fd392e5bdd76717?src=pr&el=desc) will **decrease** coverage by `0.05%`.  
> The diff coverage is `100%`.  
  
[![Impacted file tree graph](https://codecov.io/gh/vinniefalco/Beast/pull/502/graphs/tree.svg?src=pr&token=Gq6MFA9JRF&width=650&height=150)](https://codecov.io/gh/vinniefalco/Beast/pull/502?src=pr&el=tree)  
  
```diff  
@@            Coverage Diff             @@  
##           master     #502      +/-   ##  
==========================================  
- Coverage   93.51%   93.46%   -0.06%       
==========================================  
  Files          96       96                
  Lines        7093     7095       +2       
==========================================  
- Hits         6633     6631       -2       
- Misses        460      464       +4  
```  
  
  
| [Impacted Files](https://codecov.io/gh/vinniefalco/Beast/pull/502?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [include/beast/http/basic\_parser.hpp](https://codecov.io/gh/vinniefalco/Beast/pull/502?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9odHRwL2Jhc2ljX3BhcnNlci5ocHA=) | `100% <ø> (ø)` | :arrow_up: |  
| [include/beast/http/impl/message.ipp](https://codecov.io/gh/vinniefalco/Beast/pull/502?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9odHRwL2ltcGwvbWVzc2FnZS5pcHA=) | `89.47% <ø> (ø)` | :arrow_up: |  
| [include/beast/http/detail/rfc7230.hpp](https://codecov.io/gh/vinniefalco/Beast/pull/502?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9odHRwL2RldGFpbC9yZmM3MjMwLmhwcA==) | `80.95% <ø> (ø)` | :arrow_up: |  
| [include/beast/core/static\_string.hpp](https://codecov.io/gh/vinniefalco/Beast/pull/502?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9jb3JlL3N0YXRpY19zdHJpbmcuaHBw) | `100% <ø> (ø)` | :arrow_up: |  
| [include/beast/core/detail/static\_string.hpp](https://codecov.io/gh/vinniefalco/Beast/pull/502?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9jb3JlL2RldGFpbC9zdGF0aWNfc3RyaW5nLmhwcA==) | `100% <ø> (ø)` | :arrow_up: |  
| [include/beast/http/detail/basic\_parsed\_list.hpp](https://codecov.io/gh/vinniefalco/Beast/pull/502?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9odHRwL2RldGFpbC9iYXNpY19wYXJzZWRfbGlzdC5ocHA=) | `100% <ø> (ø)` | :arrow_up: |  
| [include/beast/websocket/rfc6455.hpp](https://codecov.io/gh/vinniefalco/Beast/pull/502?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC93ZWJzb2NrZXQvcmZjNjQ1NS5ocHA=) | `100% <ø> (ø)` | :arrow_up: |  
| [include/beast/http/message.hpp](https://codecov.io/gh/vinniefalco/Beast/pull/502?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9odHRwL21lc3NhZ2UuaHBw) | `100% <ø> (ø)` | :arrow_up: |  
| [include/beast/websocket/detail/hybi13.hpp](https://codecov.io/gh/vinniefalco/Beast/pull/502?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC93ZWJzb2NrZXQvZGV0YWlsL2h5YmkxMy5ocHA=) | `100% <ø> (ø)` | :arrow_up: |  
| [include/beast/core/string\_param.hpp](https://codecov.io/gh/vinniefalco/Beast/pull/502?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9jb3JlL3N0cmluZ19wYXJhbS5ocHA=) | `100% <ø> (ø)` | :arrow_up: |  
| ... and [17 more](https://codecov.io/gh/vinniefalco/Beast/pull/502?src=pr&el=tree-more) | |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/vinniefalco/Beast/pull/502?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/vinniefalco/Beast/pull/502?src=pr&el=footer). Last update [4e4bcf8...7218b64](https://codecov.io/gh/vinniefalco/Beast/pull/502?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---

## Comment 2

> Username: vinniefalco  
> Created_at: 2017-06-17 02:33:08 UTC  
> Url: https://github.com/boostorg/beast/pull/502#issuecomment-309187624  

Merged

---
