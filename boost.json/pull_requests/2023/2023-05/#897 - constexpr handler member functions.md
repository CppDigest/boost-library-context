# #897 constexpr handler member functions [Closed]

> Username: grisumbras  
> Created at: 2023-05-26 16:37:57 UTC  
> Updated at: 2023-06-02 04:34:45 UTC  
> Closed at: 2023-06-02 04:34:38 UTC  
> Url: https://github.com/boostorg/json/pull/897  



---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2023-05-26 17:48:20 UTC  
> Updated_at: 2023-05-26 17:50:15 UTC  
> Url: https://github.com/boostorg/json/pull/897#issuecomment-1564724123  

## [Codecov](https://app.codecov.io/gh/boostorg/json/pull/897?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#897](https://app.codecov.io/gh/boostorg/json/pull/897?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (288d64e) into [develop](https://app.codecov.io/gh/boostorg/json/commit/a6126253ea0486340b999530ab46e6dd5ccd11c0?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (a612625) will **not change** coverage.  
> The diff coverage is `100.00%`.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/json/pull/897/graphs/tree.svg?width=650&height=150&src=pr&token=HNiMmIjyKi&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/json/pull/897?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@           Coverage Diff            @@  
##           develop     #897   +/-   ##  
========================================  
  Coverage    99.25%   99.25%             
========================================  
  Files           71       71             
  Lines         6844     6844             
========================================  
  Hits          6793     6793             
  Misses          51       51             
```  
  
  
| [Impacted Files](https://app.codecov.io/gh/boostorg/json/pull/897?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/json/detail/impl/handler.ipp](https://app.codecov.io/gh/boostorg/json/pull/897?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9qc29uL2RldGFpbC9pbXBsL2hhbmRsZXIuaXBw) | `100.00% <100.00%> (ø)` | |  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/json/pull/897?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/json/pull/897?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [a612625...288d64e](https://app.codecov.io/gh/boostorg/json/pull/897?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---

## Comment 2

> Username: cppalliance-bot  
> Created_at: 2023-05-26 20:40:21 UTC  
> Url: https://github.com/boostorg/json/pull/897#issuecomment-1564923188  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/897/pullrequest-condensed-3141934.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/897/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/897/pullrequest.html)

---

## Comment 3

> Username: grisumbras  
> Created_at: 2023-06-01 11:31:14 UTC  
> Url: https://github.com/boostorg/json/pull/897#issuecomment-1571877739  

@pdimov do you think there's any utility to this change? It seems that compilers can see that the handler's member functions never return false any way.

---

## Comment 4

> Username: pdimov  
> Created_at: 2023-06-01 12:53:33 UTC  
> Url: https://github.com/boostorg/json/pull/897#issuecomment-1571999596  

I wouldn't expect it to make any difference.

---
