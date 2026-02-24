# #1303 Properly cancel the timer in timeout_service [Closed]

> Username: djarek  
> Created at: 2018-11-14 18:26:31 UTC  
> Updated at: 2019-09-10 21:02:23 UTC  
> Closed at: 2018-11-27 16:35:32 UTC  
> Url: https://github.com/boostorg/beast/pull/1303  

The lambda was never invoked, because at that point the Executor was already stopped.

---

## Review 1 [Commented]

> Username: vinniefalco  
> Created_at: 2018-11-14 18:42:03 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/1303#pullrequestreview-175017284  

📁 include/boost/beast/experimental/core/detail/impl/timeout_service.ipp

```diff
 168 |+     // When shutdown() is called the ExecutionContext must already be in a
 169 |+     // stopped state, so it's safe to just cancel() without synchronization.
 170 |+     timer_.cancel();
```

> Username: vinniefalco  
> Created_at: 2018-11-14 18:42:02 UTC  
> Updated_at: 2018-11-14 18:42:03 UTC  
> Url: https://github.com/boostorg/beast/pull/1303#discussion_r233570896  

LOL... whoops :)


---

## Comment 2

> Username: vinniefalco  
> Created_at: 2018-11-14 18:42:24 UTC  
> Url: https://github.com/boostorg/beast/pull/1303#issuecomment-438772299  

So does this mean that there is no race?

---

## Comment 3

> Username: djarek  
> Created_at: 2018-11-14 18:49:22 UTC  
> Url: https://github.com/boostorg/beast/pull/1303#issuecomment-438774810  

I believe there's still a data race on the timer here: https://github.com/boostorg/beast/pull/1303/files#diff-793414e6f06bada9618b29c958450224L69

---

## Comment 4

> Username: codecov[bot]  
> Created_at: 2018-11-14 19:51:10 UTC  
> Url: https://github.com/boostorg/beast/pull/1303#issuecomment-438795248  

# [Codecov](https://codecov.io/gh/boostorg/beast/pull/1303?src=pr&el=h1) Report  
> Merging [#1303](https://codecov.io/gh/boostorg/beast/pull/1303?src=pr&el=desc) into [develop](https://codecov.io/gh/boostorg/beast/commit/1ca690db0615a170c155683968071ee9eb609bc0?src=pr&el=desc) will **not change** coverage.  
> The diff coverage is `n/a`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/beast/pull/1303/graphs/tree.svg?width=650&token=Gq6MFA9JRF&height=150&src=pr)](https://codecov.io/gh/boostorg/beast/pull/1303?src=pr&el=tree)  
  
```diff  
@@           Coverage Diff            @@  
##           develop    #1303   +/-   ##  
========================================  
  Coverage    95.36%   95.36%             
========================================  
  Files          113      113             
  Lines        11117    11117             
========================================  
  Hits         10602    10602             
  Misses         515      515  
```  
  
  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/beast/pull/1303?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/beast/pull/1303?src=pr&el=footer). Last update [1ca690d...70cb949](https://codecov.io/gh/boostorg/beast/pull/1303?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---

## Comment 5

> Username: vinniefalco  
> Created_at: 2018-11-23 04:54:59 UTC  
> Url: https://github.com/boostorg/beast/pull/1303#issuecomment-441156341  

This needs proper analysis for the race

---

## Comment 6

> Username: vinniefalco  
> Created_at: 2018-11-27 16:35:32 UTC  
> Url: https://github.com/boostorg/beast/pull/1303#issuecomment-442126096  

I think it is fixed now

---
