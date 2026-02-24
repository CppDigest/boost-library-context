# #1002 fix memory leak in advanced server examples [Closed]

> Username: bebuch  
> Created at: 2018-01-31 11:25:51 UTC  
> Updated at: 2018-02-16 20:39:28 UTC  
> Closed at: 2018-02-16 20:39:28 UTC  
> Url: https://github.com/boostorg/beast/pull/1002  

emplace_back may throw and at this time the pointer created with new is a raw unmanaged pointer that will never be deleted.  
  
Beast is C++11, unfortunately the simple solution make_unique is introduced by C++14.

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2018-01-31 11:45:22 UTC  
> Updated_at: 2018-02-13 11:01:36 UTC  
> Url: https://github.com/boostorg/beast/pull/1002#issuecomment-361908532  

# [Codecov](https://codecov.io/gh/boostorg/beast/pull/1002?src=pr&el=h1) Report  
> Merging [#1002](https://codecov.io/gh/boostorg/beast/pull/1002?src=pr&el=desc) into [develop](https://codecov.io/gh/boostorg/beast/commit/908f84cb3c023ed7c5bd19e2e5283d3218a6d820?src=pr&el=desc) will **not change** coverage.  
> The diff coverage is `100%`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/beast/pull/1002/graphs/tree.svg?token=Gq6MFA9JRF&width=650&src=pr&height=150)](https://codecov.io/gh/boostorg/beast/pull/1002?src=pr&el=tree)  
  
```diff  
@@           Coverage Diff            @@  
##           develop    #1002   +/-   ##  
========================================  
  Coverage    95.43%   95.43%             
========================================  
  Files          106      106             
  Lines        10614    10614             
========================================  
  Hits         10130    10130             
  Misses         484      484  
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/beast/pull/1002?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [include/boost/beast/websocket/impl/teardown.ipp](https://codecov.io/gh/boostorg/beast/pull/1002/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC93ZWJzb2NrZXQvaW1wbC90ZWFyZG93bi5pcHA=) | `100% <ø> (ø)` | :arrow_up: |  
| [include/boost/beast/http/impl/serializer.ipp](https://codecov.io/gh/boostorg/beast/pull/1002/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9odHRwL2ltcGwvc2VyaWFsaXplci5pcHA=) | `94.71% <ø> (ø)` | :arrow_up: |  
| [include/boost/beast/websocket/detail/mask.hpp](https://codecov.io/gh/boostorg/beast/pull/1002/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC93ZWJzb2NrZXQvZGV0YWlsL21hc2suaHBw) | `100% <100%> (ø)` | :arrow_up: |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/beast/pull/1002?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/beast/pull/1002?src=pr&el=footer). Last update [908f84c...501a1b6](https://codecov.io/gh/boostorg/beast/pull/1002?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---

## Review 2 [Changes requested]

> Username: glenfe  
> Created_at: 2018-01-31 14:53:22 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/beast/pull/1002#pullrequestreview-92949617  

Just include `<boost/make_unique.hpp>` and use `boost::make_unique` here. No need for conditional compilation.

---

## Comment 3

> Username: bebuch  
> Created_at: 2018-01-31 15:24:20 UTC  
> Url: https://github.com/boostorg/beast/pull/1002#issuecomment-361965752  

Ah, of course, thank you! I will change the request. :-)

---

## Comment 4

> Username: vinniefalco  
> Created_at: 2018-02-16 20:39:28 UTC  
> Url: https://github.com/boostorg/beast/pull/1002#issuecomment-366351908  

Merged to v154, thanks!

---
