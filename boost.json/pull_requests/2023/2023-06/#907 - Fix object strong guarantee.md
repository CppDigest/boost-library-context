# #907 Fix object strong guarantee [Merged]

> Username: grisumbras  
> Created at: 2023-06-10 21:07:18 UTC  
> Updated at: 2023-06-14 10:12:40 UTC  
> Merged at: 2023-06-14 10:12:30 UTC  
> Closed at: 2023-06-14 10:12:30 UTC  
> Url: https://github.com/boostorg/json/pull/907  

Fix #763

---

## Comment 1

> Username: vinniefalco  
> Created_at: 2023-06-10 21:08:56 UTC  
> Updated_at: 2023-06-10 21:09:05 UTC  
> Url: https://github.com/boostorg/json/pull/907#issuecomment-1585816498  

There is no requirement that capacity is restored to the previous value on an exception.. it is ok to increase capacity and then later fail the insertion - and the object is returned to the caller with the increased capacity.

---

## Comment 2

> Username: grisumbras  
> Created_at: 2023-06-10 21:12:02 UTC  
> Updated_at: 2023-06-10 21:12:15 UTC  
> Url: https://github.com/boostorg/json/pull/907#issuecomment-1585816947  

If new storage is allocated, then iterators are invalidated and thus, the function doesn't provide the strong guarantee.

---

## Comment 3

> Username: grisumbras  
> Created_at: 2023-06-10 21:13:37 UTC  
> Url: https://github.com/boostorg/json/pull/907#issuecomment-1585817238  

It's not too hard to revert storage change in our case, so I don't see why we shouldn't. Strong guarantee is a good property.

---

## Comment 4

> Username: cppalliance-bot  
> Created_at: 2023-06-10 22:20:48 UTC  
> Url: https://github.com/boostorg/json/pull/907#issuecomment-1585835141  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/907/pullrequest-condensed-80ca1b6.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/907/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/907/pullrequest.html)

---

## Comment 5

> Username: cppalliance-bot  
> Created_at: 2023-06-13 21:57:59 UTC  
> Url: https://github.com/boostorg/json/pull/907#issuecomment-1590076678  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/907/pullrequest-condensed-82c4ce8.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/907/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/907/pullrequest.html)

---

## Comment 6

> Username: codecov[bot]  
> Created_at: 2023-06-14 07:12:27 UTC  
> Updated_at: 2023-06-14 07:12:31 UTC  
> Url: https://github.com/boostorg/json/pull/907#issuecomment-1590608481  

## [Codecov](https://app.codecov.io/gh/boostorg/json/pull/907?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#907](https://app.codecov.io/gh/boostorg/json/pull/907?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (8957955) into [develop](https://app.codecov.io/gh/boostorg/json/commit/4700500920e7ad05a174b98d1e6bb36a0e6548ce?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (4700500) will **increase** coverage by `0.00%`.  
> The diff coverage is `100.00%`.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/json/pull/907/graphs/tree.svg?width=650&height=150&src=pr&token=HNiMmIjyKi&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/json/pull/907?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@           Coverage Diff            @@  
##           develop     #907   +/-   ##  
========================================  
  Coverage    92.98%   92.98%             
========================================  
  Files           85       85             
  Lines         8056     8058    +2       
========================================  
+ Hits          7491     7493    +2       
  Misses         565      565             
```  
  
  
| [Impacted Files](https://app.codecov.io/gh/boostorg/json/pull/907?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/json/object.hpp](https://app.codecov.io/gh/boostorg/json/pull/907?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9qc29uL29iamVjdC5ocHA=) | `100.00% <ø> (ø)` | |  
| [include/boost/json/impl/object.hpp](https://app.codecov.io/gh/boostorg/json/pull/907?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9qc29uL2ltcGwvb2JqZWN0LmhwcA==) | `100.00% <100.00%> (ø)` | |  
| [include/boost/json/impl/object.ipp](https://app.codecov.io/gh/boostorg/json/pull/907?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9qc29uL2ltcGwvb2JqZWN0LmlwcA==) | `100.00% <100.00%> (ø)` | |  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/json/pull/907?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/json/pull/907?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [4700500...8957955](https://app.codecov.io/gh/boostorg/json/pull/907?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---

## Comment 7

> Username: cppalliance-bot  
> Created_at: 2023-06-14 08:10:40 UTC  
> Url: https://github.com/boostorg/json/pull/907#issuecomment-1590693283  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/907/pullrequest-condensed-de91d14.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/907/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/907/pullrequest.html)

---
