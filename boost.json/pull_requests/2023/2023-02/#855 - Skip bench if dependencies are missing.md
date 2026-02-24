# #855 Skip bench if dependencies are missing. [Closed]

> Username: grafikrobot  
> Created at: 2023-02-08 01:06:53 UTC  
> Updated at: 2023-02-12 15:33:23 UTC  
> Closed at: 2023-02-12 15:33:05 UTC  
> Url: https://github.com/boostorg/json/pull/855  

If one exec b2 at the json-root without running the clone.sh script one will get header-not-found errors. This change prevents the bench from building when the required extra source files are not present.

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2023-02-08 01:26:10 UTC  
> Updated_at: 2023-02-08 01:36:33 UTC  
> Url: https://github.com/boostorg/json/pull/855#issuecomment-1421779111  

# [Codecov](https://codecov.io/gh/boostorg/json/pull/855?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#855](https://codecov.io/gh/boostorg/json/pull/855?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (c448b7b) into [develop](https://codecov.io/gh/boostorg/json/commit/22c1b391d4fe8b35a5c5637f8677d6d619a25e6e?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (22c1b39) will **not change** coverage.  
> The diff coverage is `n/a`.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/json/pull/855/graphs/tree.svg?width=650&height=150&src=pr&token=HNiMmIjyKi&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://codecov.io/gh/boostorg/json/pull/855?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@           Coverage Diff            @@  
##           develop     #855   +/-   ##  
========================================  
  Coverage    99.19%   99.19%             
========================================  
  Files           70       70             
  Lines         6849     6849             
========================================  
  Hits          6794     6794             
  Misses          55       55             
```  
  
  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/json/pull/855?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/json/pull/855?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [22c1b39...c448b7b](https://codecov.io/gh/boostorg/json/pull/855?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---

## Comment 2

> Username: cppalliance-bot  
> Created_at: 2023-02-08 02:19:51 UTC  
> Url: https://github.com/boostorg/json/pull/855#issuecomment-1421883359  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/855/pullrequest-condensed-2027800.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/855/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/855/pullrequest.html)

---

## Comment 3

> Username: grisumbras  
> Created_at: 2023-02-08 07:41:01 UTC  
> Url: https://github.com/boostorg/json/pull/855#issuecomment-1422166459  

What about just making the target explicit?

---

## Comment 4

> Username: grisumbras  
> Created_at: 2023-02-08 07:43:34 UTC  
> Url: https://github.com/boostorg/json/pull/855#issuecomment-1422168725  

Or removing `build-project bench ;` from the root Jamfile?

---

## Comment 5

> Username: grisumbras  
> Created_at: 2023-02-12 14:47:07 UTC  
> Url: https://github.com/boostorg/json/pull/855#issuecomment-1427051707  

I've merged alternative fix in #857

---
