# #789 Fix writing headers into std::ostream [Closed]

> Username: inetic  
> Created at: 2017-09-21 15:31:19 UTC  
> Updated at: 2017-09-29 13:23:15 UTC  
> Closed at: 2017-09-29 13:23:15 UTC  
> Url: https://github.com/boostorg/beast/pull/789  



---

## Comment 1

> Username: vinniefalco  
> Created_at: 2017-09-21 18:26:00 UTC  
> Url: https://github.com/boostorg/beast/pull/789#issuecomment-331241575  

Whoops! I wonder why clang and gcc didn't generate a compile error?

---

## Comment 2

> Username: vinniefalco  
> Created_at: 2017-09-21 18:27:29 UTC  
> Updated_at: 2017-09-21 18:27:48 UTC  
> Url: https://github.com/boostorg/beast/pull/789#issuecomment-331241972  

Ah, probably because it is a template function and it is not called! I appreciate the pull request.  
  
Just a hint, but if you want this pull request to be even more amazing, consider adding:  
  
* A small test (in test/beast/http/write.cpp) which constructs a message and writes it to a `std::stringstream`, checks the result.  
  
Or I can add it, up to you!

---

## Comment 3

> Username: vinniefalco  
> Created_at: 2017-09-22 00:55:52 UTC  
> Url: https://github.com/boostorg/beast/pull/789#issuecomment-331321053  

Your commit is part of version 118: https://github.com/boostorg/beast/pull/794

---

## Comment 4

> Username: inetic  
> Created_at: 2017-09-22 07:12:30 UTC  
> Url: https://github.com/boostorg/beast/pull/789#issuecomment-331369467  

Thanks. You're right, a test for it would be nice. I'll try to add one when time permits.

---

## Comment 5

> Username: vinniefalco  
> Created_at: 2017-09-22 08:09:18 UTC  
> Url: https://github.com/boostorg/beast/pull/789#issuecomment-331380899  

@inetic I already added one, it was easy - thanks!

---

## Comment 6

> Username: codecov[bot]  
> Created_at: 2017-09-23 01:40:45 UTC  
> Url: https://github.com/boostorg/beast/pull/789#issuecomment-331599692  

# [Codecov](https://codecov.io/gh/boostorg/beast/pull/789?src=pr&el=h1) Report  
> Merging [#789](https://codecov.io/gh/boostorg/beast/pull/789?src=pr&el=desc) into [master](https://codecov.io/gh/boostorg/beast/commit/dca65932a8055dd3e240633c6a058db8aa7f7915?src=pr&el=desc) will **not change** coverage.  
> The diff coverage is `n/a`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/beast/pull/789/graphs/tree.svg?height=150&token=Gq6MFA9JRF&src=pr&width=650)](https://codecov.io/gh/boostorg/beast/pull/789?src=pr&el=tree)  
  
```diff  
@@          Coverage Diff           @@  
##           master    #789   +/-   ##  
======================================  
  Coverage    95.7%   95.7%             
======================================  
  Files         104     104             
  Lines       10531   10531             
======================================  
  Hits        10079   10079             
  Misses        452     452  
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/beast/pull/789?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [include/boost/beast/http/impl/write.ipp](https://codecov.io/gh/boostorg/beast/pull/789?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9odHRwL2ltcGwvd3JpdGUuaXBw) | `93.44% <ø> (ø)` | :arrow_up: |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/beast/pull/789?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/beast/pull/789?src=pr&el=footer). Last update [dca6593...403eb61](https://codecov.io/gh/boostorg/beast/pull/789?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---

## Comment 7

> Username: vinniefalco  
> Created_at: 2017-09-23 12:49:14 UTC  
> Url: https://github.com/boostorg/beast/pull/789#issuecomment-331631800  

These examples also have an extraneous strand for listener:  
  
* http-server-async-ssl  
* websocket-server-async  
* websocket-server-async-ssl

---

## Comment 8

> Username: vinniefalco  
> Created_at: 2017-09-29 13:23:15 UTC  
> Url: https://github.com/boostorg/beast/pull/789#issuecomment-333124903  

Merged, thanks!

---
