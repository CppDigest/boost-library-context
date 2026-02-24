# #583 Version 72 [Closed]

> Username: vinniefalco  
> Created at: 2017-07-04 00:57:05 UTC  
> Updated at: 2017-07-05 04:23:39 UTC  
> Closed at: 2017-07-05 01:26:04 UTC  
> Url: https://github.com/boostorg/beast/pull/583  

HTTP:  
  
* Tidy up set payload in http-server-fast  
* Refine Body::size specification  
* Newly constructed responses have a 200 OK result  
* Add http-server-threaded example  
* Various improvements to http_server_fast.cpp  
  
WebSocket:  
  
* Add websocket-server-async example  
* Documentation tidying  
  
Documentation preview:  
http://vinniefalco.github.io/stage/beast/v72/

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2017-07-04 01:44:25 UTC  
> Updated_at: 2017-07-05 00:47:42 UTC  
> Url: https://github.com/boostorg/beast/pull/583#issuecomment-312760864  

# [Codecov](https://codecov.io/gh/vinniefalco/Beast/pull/583?src=pr&el=h1) Report  
> Merging [#583](https://codecov.io/gh/vinniefalco/Beast/pull/583?src=pr&el=desc) into [develop](https://codecov.io/gh/vinniefalco/Beast/commit/52cefbcbe3447abee889a596d92edafc4e4fac3c?src=pr&el=desc) will **not change** coverage.  
> The diff coverage is `n/a`.  
  
[![Impacted file tree graph](https://codecov.io/gh/vinniefalco/Beast/pull/583/graphs/tree.svg?src=pr&token=Gq6MFA9JRF&width=650&height=150)](https://codecov.io/gh/vinniefalco/Beast/pull/583?src=pr&el=tree)  
  
```diff  
@@           Coverage Diff            @@  
##           develop     #583   +/-   ##  
========================================  
  Coverage    93.79%   93.79%             
========================================  
  Files           94       94             
  Lines         7368     7368             
========================================  
  Hits          6911     6911             
  Misses         457      457  
```  
  
  
| [Impacted Files](https://codecov.io/gh/vinniefalco/Beast/pull/583?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [include/beast/http/message.hpp](https://codecov.io/gh/vinniefalco/Beast/pull/583?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9odHRwL21lc3NhZ2UuaHBw) | `100% <ø> (ø)` | :arrow_up: |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/vinniefalco/Beast/pull/583?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/vinniefalco/Beast/pull/583?src=pr&el=footer). Last update [52cefbc...5c727ed](https://codecov.io/gh/vinniefalco/Beast/pull/583?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---

## Comment 2

> Username: vinniefalco  
> Created_at: 2017-07-05 01:26:04 UTC  
> Url: https://github.com/boostorg/beast/pull/583#issuecomment-312975145  

Merged

---
