# #2019 Update WebSocket examples to set TLS SNI [Closed]

> Username: madmongo1  
> Created at: 2020-07-16 08:17:14 UTC  
> Updated at: 2020-08-29 09:42:53 UTC  
> Closed at: 2020-08-29 09:42:53 UTC  
> Url: https://github.com/boostorg/beast/pull/2019  



---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2020-07-16 19:12:44 UTC  
> Updated_at: 2020-07-17 16:15:31 UTC  
> Url: https://github.com/boostorg/beast/pull/2019#issuecomment-659614356  

# [Codecov](https://codecov.io/gh/boostorg/beast/pull/2019?src=pr&el=h1) Report  
> Merging [#2019](https://codecov.io/gh/boostorg/beast/pull/2019?src=pr&el=desc) into [develop](https://codecov.io/gh/boostorg/beast/commit/3486e9cb18aa39b392e07031a33e65b1792fbccf&el=desc) will **not change** coverage.  
> The diff coverage is `n/a`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/beast/pull/2019/graphs/tree.svg?width=650&height=150&src=pr&token=Gq6MFA9JRF)](https://codecov.io/gh/boostorg/beast/pull/2019?src=pr&el=tree)  
  
```diff  
@@           Coverage Diff            @@  
##           develop    #2019   +/-   ##  
========================================  
  Coverage    95.09%   95.09%             
========================================  
  Files          153      153             
  Lines        11939    11939             
========================================  
  Hits         11353    11353             
  Misses         586      586             
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/beast/pull/2019?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [include/boost/beast/websocket/impl/ping.hpp](https://codecov.io/gh/boostorg/beast/pull/2019/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC93ZWJzb2NrZXQvaW1wbC9waW5nLmhwcA==) | `96.42% <0.00%> (-0.90%)` | :arrow_down: |  
| [include/boost/beast/zlib/detail/inflate\_stream.ipp](https://codecov.io/gh/boostorg/beast/pull/2019/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC96bGliL2RldGFpbC9pbmZsYXRlX3N0cmVhbS5pcHA=) | `94.57% <0.00%> (+0.19%)` | :arrow_up: |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/beast/pull/2019?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/beast/pull/2019?src=pr&el=footer). Last update [3486e9c...ee782d4](https://codecov.io/gh/boostorg/beast/pull/2019?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---

## Review 2 [Commented]

> Username: vinniefalco  
> Created_at: 2020-07-18 15:35:42 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/2019#pullrequestreview-451064450  

📁 example/websocket/client/async-ssl/websocket_client_async_ssl.cpp

```diff
 120 |+                 host_.c_str()))
 121 |+         {
 122 |+             ec = beast::error_code(static_cast<int>(::ERR_get_error()),
```

> Username: vinniefalco  
> Created_at: 2020-07-18 15:35:41 UTC  
> Updated_at: 2020-07-18 15:35:42 UTC  
> Url: https://github.com/boostorg/beast/pull/2019#discussion_r456800993  

Do all the examples which call `SSL_set_tlsext_host_name` also call `::ERR_get_error`?

> Username: madmongo1  
> Created_at: 2020-07-20 08:38:52 UTC  
> Url: https://github.com/boostorg/beast/pull/2019#discussion_r457180672  

Yes.


---

## Comment 3

> Username: stale[bot]  
> Created_at: 2020-08-22 19:00:34 UTC  
> Url: https://github.com/boostorg/beast/pull/2019#issuecomment-678678912  

This issue has been open for a while with no activity, has it been resolved?

---

## Comment 4

> Username: madmongo1  
> Created_at: 2020-08-29 09:42:52 UTC  
> Url: https://github.com/boostorg/beast/pull/2019#issuecomment-683266018  

merged with #2037

---
