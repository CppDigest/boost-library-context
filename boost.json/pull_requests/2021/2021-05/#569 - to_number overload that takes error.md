# #569 to_number overload that takes error [Merged]

> Username: grisumbras  
> Created at: 2021-05-07 21:10:20 UTC  
> Updated at: 2021-05-08 12:30:00 UTC  
> Merged at: 2021-05-08 12:29:10 UTC  
> Closed at: 2021-05-08 12:29:10 UTC  
> Url: https://github.com/boostorg/json/pull/569  

Fix #548

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2021-05-07 21:15:52 UTC  
> Updated_at: 2021-05-07 22:03:01 UTC  
> Url: https://github.com/boostorg/json/pull/569#issuecomment-834785429  

# [Codecov](https://codecov.io/gh/boostorg/json/pull/569?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#569](https://codecov.io/gh/boostorg/json/pull/569?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (43d65fc) into [develop](https://codecov.io/gh/boostorg/json/commit/94276c9d6e885279631ff1d26eead66531b56fb6?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (94276c9) will **increase** coverage by `0.00%`.  
> The diff coverage is `100.00%`.  
  
> :exclamation: Current head 43d65fc differs from pull request most recent head 2ebbd0c. Consider uploading reports for the commit 2ebbd0c to get more accurate results  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/json/pull/569/graphs/tree.svg?width=650&height=150&src=pr&token=HNiMmIjyKi&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://codecov.io/gh/boostorg/json/pull/569?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@           Coverage Diff            @@  
##           develop     #569   +/-   ##  
========================================  
  Coverage    99.11%   99.11%             
========================================  
  Files           68       68             
  Lines         6104     6110    +6       
========================================  
+ Hits          6050     6056    +6       
  Misses          54       54             
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/json/pull/569?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/json/detail/impl/except.ipp](https://codecov.io/gh/boostorg/json/pull/569/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9qc29uL2RldGFpbC9pbXBsL2V4Y2VwdC5pcHA=) | `100.00% <100.00%> (ø)` | |  
| [include/boost/json/value.hpp](https://codecov.io/gh/boostorg/json/pull/569/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9qc29uL3ZhbHVlLmhwcA==) | `99.79% <100.00%> (+<0.01%)` | :arrow_up: |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/json/pull/569?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/json/pull/569?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [94276c9...2ebbd0c](https://codecov.io/gh/boostorg/json/pull/569?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).

---

## Comment 2

> Username: vinniefalco  
> Created_at: 2021-05-07 21:50:37 UTC  
> Url: https://github.com/boostorg/json/pull/569#issuecomment-834804451  

Commit message could read "to_number overload takes error"

---

## Review 3 [Approved]

> Username: pdimov  
> Created_at: 2021-05-07 22:05:01 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/json/pull/569#pullrequestreview-654898707  

LGTM

---

## Comment 4

> Username: cppalliance-bot  
> Created_at: 2021-05-07 23:14:03 UTC  
> Url: https://github.com/boostorg/json/pull/569#issuecomment-834851836  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/569/pullrequest-condensed-3d4dd7f.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/569/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/569/pullrequest.html)

---
