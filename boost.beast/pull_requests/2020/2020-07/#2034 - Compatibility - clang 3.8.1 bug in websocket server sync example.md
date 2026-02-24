# #2034 Compatibility - clang 3.8.1 bug in websocket server sync example [Closed]

> Username: madmongo1  
> Created at: 2020-07-23 09:13:48 UTC  
> Updated at: 2020-07-24 12:06:55 UTC  
> Closed at: 2020-07-24 12:06:55 UTC  
> Url: https://github.com/boostorg/beast/pull/2034  

fixes #2032  
  
Clang 3.8.1 was unhappy with the by-move capture of a socket in `std::thread(std::bind(`.  
  
Thanks to @pdimov for suggesting a much more elegant solution.

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2020-07-23 11:02:59 UTC  
> Updated_at: 2020-07-24 10:56:09 UTC  
> Url: https://github.com/boostorg/beast/pull/2034#issuecomment-662944849  

# [Codecov](https://codecov.io/gh/boostorg/beast/pull/2034?src=pr&el=h1) Report  
> Merging [#2034](https://codecov.io/gh/boostorg/beast/pull/2034?src=pr&el=desc) into [develop](https://codecov.io/gh/boostorg/beast/commit/3486e9cb18aa39b392e07031a33e65b1792fbccf&el=desc) will **not change** coverage.  
> The diff coverage is `n/a`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/beast/pull/2034/graphs/tree.svg?width=650&height=150&src=pr&token=Gq6MFA9JRF)](https://codecov.io/gh/boostorg/beast/pull/2034?src=pr&el=tree)  
  
```diff  
@@           Coverage Diff            @@  
##           develop    #2034   +/-   ##  
========================================  
  Coverage    95.09%   95.09%             
========================================  
  Files          153      153             
  Lines        11939    11939             
========================================  
  Hits         11353    11353             
  Misses         586      586             
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/beast/pull/2034?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [include/boost/beast/websocket/impl/ping.hpp](https://codecov.io/gh/boostorg/beast/pull/2034/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC93ZWJzb2NrZXQvaW1wbC9waW5nLmhwcA==) | `96.42% <0.00%> (-0.90%)` | :arrow_down: |  
| [include/boost/beast/zlib/detail/inflate\_stream.ipp](https://codecov.io/gh/boostorg/beast/pull/2034/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC96bGliL2RldGFpbC9pbmZsYXRlX3N0cmVhbS5pcHA=) | `94.57% <0.00%> (+0.19%)` | :arrow_up: |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/beast/pull/2034?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/beast/pull/2034?src=pr&el=footer). Last update [3486e9c...561a541](https://codecov.io/gh/boostorg/beast/pull/2034?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---

## Review 2 [Commented]

> Username: vinniefalco  
> Created_at: 2020-07-24 11:48:08 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/2034#pullrequestreview-454823902  

📁 example/websocket/server/sync/websocket_server_sync.cpp

```diff
 111 |+             std::thread{
 112 |+                 &do_session, std::move(socket)
 113 |+             }.detach();
```

> Username: vinniefalco  
> Created_at: 2020-07-24 11:48:08 UTC  
> Url: https://github.com/boostorg/beast/pull/2034#discussion_r460004656  

Style:  
```  
std::thread(  
    &do_session,  
    std::move(socket)).detach();  
```

> Username: madmongo1  
> Created_at: 2020-07-24 11:55:22 UTC  
> Url: https://github.com/boostorg/beast/pull/2034#discussion_r460007669  

I copied the curly braces from the existing code :/

> Username: vinniefalco  
> Created_at: 2020-07-24 11:59:30 UTC  
> Updated_at: 2020-07-24 11:59:47 UTC  
> Url: https://github.com/boostorg/beast/pull/2034#discussion_r460009330  

Yeah the curly braces were dumb. I was going through a phase.


---
