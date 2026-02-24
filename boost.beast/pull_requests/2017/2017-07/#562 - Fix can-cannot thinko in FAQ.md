# #562 Fix can/cannot thinko in FAQ. [Closed]

> Username: tzlaine  
> Created at: 2017-07-01 19:40:56 UTC  
> Updated at: 2017-07-03 21:51:56 UTC  
> Closed at: 2017-07-03 21:51:56 UTC  
> Url: https://github.com/boostorg/beast/pull/562  

Regarding HTTP/2, the FAQ says:  
"Users cannot work with HTTP/1 now; we should not deny them that functionality today to wait for a newer protocol tomorrow."  
  
I think you meant "Users *can* work with HTTP/1 now; we should not deny them that...".

---

## Comment 1

> Username: vinniefalco  
> Created_at: 2017-07-01 20:01:03 UTC  
> Url: https://github.com/boostorg/beast/pull/562#issuecomment-312452769  

What I mean is that there is currently no standard solution for working with the HTTP protocol, which is true (not counting Beast). But your statement is also true. I will merge it

---

## Comment 2

> Username: codecov[bot]  
> Created_at: 2017-07-01 20:05:57 UTC  
> Updated_at: 2017-07-01 20:06:17 UTC  
> Url: https://github.com/boostorg/beast/pull/562#issuecomment-312452982  

# [Codecov](https://codecov.io/gh/vinniefalco/Beast/pull/562?src=pr&el=h1) Report  
> Merging [#562](https://codecov.io/gh/vinniefalco/Beast/pull/562?src=pr&el=desc) into [review](https://codecov.io/gh/vinniefalco/Beast/commit/78a065ba39836d91d7e70d93de7f9140f518083b?src=pr&el=desc) will **increase** coverage by `0.05%`.  
> The diff coverage is `n/a`.  
  
[![Impacted file tree graph](https://codecov.io/gh/vinniefalco/Beast/pull/562/graphs/tree.svg?token=Gq6MFA9JRF&src=pr&height=150&width=650)](https://codecov.io/gh/vinniefalco/Beast/pull/562?src=pr&el=tree)  
  
```diff  
@@            Coverage Diff             @@  
##           review     #562      +/-   ##  
==========================================  
+ Coverage   93.77%   93.83%   +0.05%       
==========================================  
  Files          94       94                
  Lines        7393     7393                
==========================================  
+ Hits         6933     6937       +4       
+ Misses        460      456       -4  
```  
  
  
| [Impacted Files](https://codecov.io/gh/vinniefalco/Beast/pull/562?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [include/beast/websocket/detail/pmd\_extension.hpp](https://codecov.io/gh/vinniefalco/Beast/pull/562?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC93ZWJzb2NrZXQvZGV0YWlsL3BtZF9leHRlbnNpb24uaHBw) | `85.62% <0%> (+1.96%)` | :arrow_up: |  
| [include/beast/http/empty\_body.hpp](https://codecov.io/gh/vinniefalco/Beast/pull/562?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9odHRwL2VtcHR5X2JvZHkuaHBw) | `71.42% <0%> (+14.28%)` | :arrow_up: |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/vinniefalco/Beast/pull/562?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/vinniefalco/Beast/pull/562?src=pr&el=footer). Last update [78a065b...aeb4590](https://codecov.io/gh/vinniefalco/Beast/pull/562?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---

## Comment 3

> Username: vinniefalco  
> Created_at: 2017-07-01 20:09:53 UTC  
> Url: https://github.com/boostorg/beast/pull/562#issuecomment-312453157  

This will go into **v71**

---
