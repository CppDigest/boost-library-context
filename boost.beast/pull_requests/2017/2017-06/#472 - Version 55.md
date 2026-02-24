# #472 Version 55 [Merged]

> Username: vinniefalco  
> Created at: 2017-06-12 09:54:44 UTC  
> Updated at: 2017-06-13 00:43:31 UTC  
> Merged at: 2017-06-12 22:58:29 UTC  
> Closed at: 2017-06-12 22:58:29 UTC  
> Url: https://github.com/boostorg/beast/pull/472  

* Don't allocate memory to handle obs-fold  
* Add Beast CMake interface target  
* Avoid a parser allocation using non-flat buffer  
* read_size replaces read_size_helper  
  
Documentation preview:  
http://vinniefalco.github.io/stage/beast/v55

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2017-06-12 10:32:51 UTC  
> Updated_at: 2017-06-12 23:34:05 UTC  
> Url: https://github.com/boostorg/beast/pull/472#issuecomment-307752165  

# [Codecov](https://codecov.io/gh/vinniefalco/Beast/pull/472?src=pr&el=h1) Report  
> Merging [#472](https://codecov.io/gh/vinniefalco/Beast/pull/472?src=pr&el=desc) into [master](https://codecov.io/gh/vinniefalco/Beast/commit/6a8912a88ba94dd28aa11b086faaf32a11c3fbf7?src=pr&el=desc) will **decrease** coverage by `0.13%`.  
> The diff coverage is `80%`.  
  
[![Impacted file tree graph](https://codecov.io/gh/vinniefalco/Beast/pull/472/graphs/tree.svg?width=650&src=pr&token=Gq6MFA9JRF&height=150)](https://codecov.io/gh/vinniefalco/Beast/pull/472?src=pr&el=tree)  
  
```diff  
@@            Coverage Diff             @@  
##           master     #472      +/-   ##  
==========================================  
- Coverage   93.78%   93.65%   -0.14%       
==========================================  
  Files          95       95                
  Lines        6939     6949      +10       
==========================================  
  Hits         6508     6508                
- Misses        431      441      +10  
```  
  
  
| [Impacted Files](https://codecov.io/gh/vinniefalco/Beast/pull/472?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [include/beast/http/fields.hpp](https://codecov.io/gh/vinniefalco/Beast/pull/472?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9odHRwL2ZpZWxkcy5ocHA=) | `100% <ø> (ø)` | :arrow_up: |  
| [include/beast/websocket/stream.hpp](https://codecov.io/gh/vinniefalco/Beast/pull/472?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC93ZWJzb2NrZXQvc3RyZWFtLmhwcA==) | `90.47% <ø> (ø)` | :arrow_up: |  
| [include/beast/http/basic\_parser.hpp](https://codecov.io/gh/vinniefalco/Beast/pull/472?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9odHRwL2Jhc2ljX3BhcnNlci5ocHA=) | `100% <ø> (ø)` | :arrow_up: |  
| [include/beast/http/impl/error.ipp](https://codecov.io/gh/vinniefalco/Beast/pull/472?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9odHRwL2ltcGwvZXJyb3IuaXBw) | `97.14% <0%> (-2.86%)` | :arrow_down: |  
| [include/beast/http/impl/read.ipp](https://codecov.io/gh/vinniefalco/Beast/pull/472?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9odHRwL2ltcGwvcmVhZC5pcHA=) | `90% <100%> (ø)` | :arrow_up: |  
| [include/beast/websocket/detail/pmd\_extension.hpp](https://codecov.io/gh/vinniefalco/Beast/pull/472?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC93ZWJzb2NrZXQvZGV0YWlsL3BtZF9leHRlbnNpb24uaHBw) | `81.81% <100%> (ø)` | :arrow_up: |  
| [include/beast/core/detail/ostream.hpp](https://codecov.io/gh/vinniefalco/Beast/pull/472?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9jb3JlL2RldGFpbC9vc3RyZWFtLmhwcA==) | `92.68% <100%> (ø)` | :arrow_up: |  
| [include/beast/http/impl/basic\_parser.ipp](https://codecov.io/gh/vinniefalco/Beast/pull/472?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9odHRwL2ltcGwvYmFzaWNfcGFyc2VyLmlwcA==) | `95.94% <76.47%> (-3.02%)` | :arrow_down: |  
| [include/beast/core/impl/read\_size.ipp](https://codecov.io/gh/vinniefalco/Beast/pull/472?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9jb3JlL2ltcGwvcmVhZF9zaXplLmlwcA==) | `91.66% <91.66%> (ø)` | |  
| [include/beast/core/drain\_buffer.hpp](https://codecov.io/gh/vinniefalco/Beast/pull/472?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9jb3JlL2RyYWluX2J1ZmZlci5ocHA=) | `100% <0%> (ø)` | :arrow_up: |  
| ... and [1 more](https://codecov.io/gh/vinniefalco/Beast/pull/472?src=pr&el=tree-more) | |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/vinniefalco/Beast/pull/472?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/vinniefalco/Beast/pull/472?src=pr&el=footer). Last update [6a8912a...fde6929](https://codecov.io/gh/vinniefalco/Beast/pull/472?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---
