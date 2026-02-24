# #1056 fix narrowing warnings [Closed]

> Username: DanielaE  
> Created at: 2018-03-03 17:46:45 UTC  
> Updated at: 2018-03-05 16:48:33 UTC  
> Closed at: 2018-03-05 16:37:18 UTC  
> Url: https://github.com/boostorg/beast/pull/1056  

Signed-off-by: Daniela Engert <dani@ngrt.de>

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2018-03-03 19:36:51 UTC  
> Url: https://github.com/boostorg/beast/pull/1056#issuecomment-370173866  

# [Codecov](https://codecov.io/gh/boostorg/beast/pull/1056?src=pr&el=h1) Report  
> Merging [#1056](https://codecov.io/gh/boostorg/beast/pull/1056?src=pr&el=desc) into [develop](https://codecov.io/gh/boostorg/beast/commit/2fd3e46b202dcd5f63bac7dab178e2d7a6b47938?src=pr&el=desc) will **not change** coverage.  
> The diff coverage is `100%`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/beast/pull/1056/graphs/tree.svg?height=150&width=650&token=Gq6MFA9JRF&src=pr)](https://codecov.io/gh/boostorg/beast/pull/1056?src=pr&el=tree)  
  
```diff  
@@           Coverage Diff            @@  
##           develop    #1056   +/-   ##  
========================================  
  Coverage    95.46%   95.46%             
========================================  
  Files          106      106             
  Lines        10646    10646             
========================================  
  Hits         10163    10163             
  Misses         483      483  
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/beast/pull/1056?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [include/boost/beast/websocket/detail/mask.hpp](https://codecov.io/gh/boostorg/beast/pull/1056/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC93ZWJzb2NrZXQvZGV0YWlsL21hc2suaHBw) | `100% <100%> (ø)` | :arrow_up: |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/beast/pull/1056?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/beast/pull/1056?src=pr&el=footer). Last update [2fd3e46...5bfe39d](https://codecov.io/gh/boostorg/beast/pull/1056?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---

## Review 2 [Commented]

> Username: vinniefalco  
> Created_at: 2018-03-04 00:59:37 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/1056#pullrequestreview-100991589  

📁 include/boost/beast/websocket/detail/mask.hpp

```diff
 126 |         for(std::size_t i = 0; i < n; ++i)
 127 |             p[i] ^= mask[i];
 128 |-         rol(key, 4 - n);
```

> Username: vinniefalco  
> Created_at: 2018-03-04 00:59:37 UTC  
> Url: https://github.com/boostorg/beast/pull/1056#discussion_r172033727  

I guess this is causing warnings for 64-bit builds?

> Username: cstratopoulos  
> Created_at: 2018-03-04 02:30:16 UTC  
> Url: https://github.com/boostorg/beast/pull/1056#discussion_r172035178  

@vinniefalco That's been my experience with x64 builds on Visual Studio with recent Beast

> Username: DanielaE  
> Created_at: 2018-03-04 06:50:32 UTC  
> Url: https://github.com/boostorg/beast/pull/1056#discussion_r172038926  

@vinniefalco : correct. I do all Boost testing on 64-bit msvc at /W4 to catch such (and similar) kinds of problems.


---

## Review 3 [Commented]

> Username: cstratopoulos  
> Created_at: 2018-03-04 02:31:34 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/1056#pullrequestreview-100993005  

Instead of casting the result `4 - n`, would it make sense to change the signature of `rol` to take `std::size_t` rather than just `unsigned`? I might be missing something but it looks like the routine is only used in this file, and hence is only ever called with `4 - n` where `n` is `std::size_t` since it comes from `boost::asio::mutable_buffer::size`

---

## Comment 4

> Username: vinniefalco  
> Created_at: 2018-03-04 22:11:26 UTC  
> Url: https://github.com/boostorg/beast/pull/1056#issuecomment-370269014  

@cstratopoulos Yes, I think that is a good idea

---

## Comment 5

> Username: vinniefalco  
> Created_at: 2018-03-05 16:37:17 UTC  
> Url: https://github.com/boostorg/beast/pull/1056#issuecomment-370480003  

Merged in 163, thank you!

---

## Comment 6

> Username: DanielaE  
> Created_at: 2018-03-05 16:48:33 UTC  
> Url: https://github.com/boostorg/beast/pull/1056#issuecomment-370483912  

Thanks, Vinnie - the narrowing warning is gone now 😄

---
