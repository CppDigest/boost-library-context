# #912 Fix type of pointer to _umul128 [Merged]

> Username: mborland  
> Created at: 2023-06-29 07:30:37 UTC  
> Updated at: 2023-06-29 17:46:24 UTC  
> Merged at: 2023-06-29 17:46:24 UTC  
> Closed at: 2023-06-29 17:46:24 UTC  
> Url: https://github.com/boostorg/json/pull/912  

`_umul128` expects a pointer of type `unsigned long long*` instead of `uint64_t*`.

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2023-06-29 08:06:51 UTC  
> Updated_at: 2023-06-29 08:08:42 UTC  
> Url: https://github.com/boostorg/json/pull/912#issuecomment-1612598284  

## [Codecov](https://app.codecov.io/gh/boostorg/json/pull/912?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#912](https://app.codecov.io/gh/boostorg/json/pull/912?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (00ba384) into [develop](https://app.codecov.io/gh/boostorg/json/commit/3d751ad9e5a6803fb4f7f7559d0a19f1c4784ce9?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (3d751ad) will **not change** coverage.  
> The diff coverage is `n/a`.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/json/pull/912/graphs/tree.svg?width=650&height=150&src=pr&token=HNiMmIjyKi&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/json/pull/912?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@           Coverage Diff            @@  
##           develop     #912   +/-   ##  
========================================  
  Coverage    92.97%   92.97%             
========================================  
  Files           85       85             
  Lines         8072     8072             
========================================  
  Hits          7505     7505             
  Misses         567      567             
```  
  
  
| [Impacted Files](https://app.codecov.io/gh/boostorg/json/pull/912?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [...detail/charconv/detail/fast\_float/float\_common.hpp](https://app.codecov.io/gh/boostorg/json/pull/912?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9qc29uL2RldGFpbC9jaGFyY29udi9kZXRhaWwvZmFzdF9mbG9hdC9mbG9hdF9jb21tb24uaHBw) | `62.19% <ø> (ø)` | |  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/json/pull/912?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/json/pull/912?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [3d751ad...00ba384](https://app.codecov.io/gh/boostorg/json/pull/912?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---

## Comment 2

> Username: grisumbras  
> Created_at: 2023-06-29 08:11:58 UTC  
> Url: https://github.com/boostorg/json/pull/912#issuecomment-1612604206  

Thanks for the patch!

---

## Comment 3

> Username: cppalliance-bot  
> Created_at: 2023-06-29 08:45:47 UTC  
> Url: https://github.com/boostorg/json/pull/912#issuecomment-1612647033  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/912/pullrequest-condensed-f4ee4ff.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/912/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/912/pullrequest.html)

---
