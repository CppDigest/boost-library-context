# #2036 Version 300 candidate [Merged]

> Username: madmongo1  
> Created at: 2020-07-24 11:44:10 UTC  
> Updated at: 2020-07-24 12:06:55 UTC  
> Merged at: 2020-07-24 12:06:55 UTC  
> Closed at: 2020-07-24 12:06:55 UTC  
> Url: https://github.com/boostorg/beast/pull/2036  

removed API-changing fixes

---

## Review 1 [Commented]

> Username: vinniefalco  
> Created_at: 2020-07-24 11:46:12 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/2036#pullrequestreview-454822903  

📁 example/websocket/server/sync/websocket_server_sync.cpp

```diff
 111 |+             std::thread{
 112 |+                 &do_session, std::move(socket)
 113 |+             }.detach();
```

> Username: vinniefalco  
> Created_at: 2020-07-24 11:46:12 UTC  
> Updated_at: 2020-07-24 12:03:39 UTC  
> Url: https://github.com/boostorg/beast/pull/2036#discussion_r460003893  

Style:  
```  
std::thread(  
    &do_session,  
    std::move(socket)).detach();  
```


---

## Comment 2

> Username: vinniefalco  
> Created_at: 2020-07-24 11:47:07 UTC  
> Url: https://github.com/boostorg/beast/pull/2036#issuecomment-663500400  

Looks benign, see note

---

## Comment 3

> Username: codecov[bot]  
> Created_at: 2020-07-24 12:00:03 UTC  
> Updated_at: 2020-07-24 12:03:47 UTC  
> Url: https://github.com/boostorg/beast/pull/2036#issuecomment-663504498  

# [Codecov](https://codecov.io/gh/boostorg/beast/pull/2036?src=pr&el=h1) Report  
> Merging [#2036](https://codecov.io/gh/boostorg/beast/pull/2036?src=pr&el=desc) into [develop](https://codecov.io/gh/boostorg/beast/commit/3486e9cb18aa39b392e07031a33e65b1792fbccf&el=desc) will **not change** coverage.  
> The diff coverage is `100.00%`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/beast/pull/2036/graphs/tree.svg?width=650&height=150&src=pr&token=Gq6MFA9JRF)](https://codecov.io/gh/boostorg/beast/pull/2036?src=pr&el=tree)  
  
```diff  
@@           Coverage Diff            @@  
##           develop    #2036   +/-   ##  
========================================  
  Coverage    95.09%   95.09%             
========================================  
  Files          153      153             
  Lines        11939    11939             
========================================  
  Hits         11353    11353             
  Misses         586      586             
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/beast/pull/2036?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [include/boost/beast/http/basic\_parser.hpp](https://codecov.io/gh/boostorg/beast/pull/2036/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9odHRwL2Jhc2ljX3BhcnNlci5ocHA=) | `94.11% <100.00%> (ø)` | |  
| [include/boost/beast/websocket/impl/ping.hpp](https://codecov.io/gh/boostorg/beast/pull/2036/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC93ZWJzb2NrZXQvaW1wbC9waW5nLmhwcA==) | `96.42% <0.00%> (-0.90%)` | :arrow_down: |  
| [include/boost/beast/zlib/detail/inflate\_stream.ipp](https://codecov.io/gh/boostorg/beast/pull/2036/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC96bGliL2RldGFpbC9pbmZsYXRlX3N0cmVhbS5pcHA=) | `94.57% <0.00%> (+0.19%)` | :arrow_up: |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/beast/pull/2036?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/beast/pull/2036?src=pr&el=footer). Last update [3486e9c...201105e](https://codecov.io/gh/boostorg/beast/pull/2036?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---
