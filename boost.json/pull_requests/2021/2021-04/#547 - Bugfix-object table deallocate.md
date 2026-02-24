# #547 Bugfix/object table deallocate [Closed]

> Username: grisumbras  
> Created at: 2021-04-25 13:24:55 UTC  
> Updated at: 2021-04-25 16:51:49 UTC  
> Closed at: 2021-04-25 16:51:46 UTC  
> Url: https://github.com/boostorg/json/pull/547  

This PR is a derivative of #544 and only exists to run CI with added test

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2021-04-25 13:41:03 UTC  
> Updated_at: 2021-04-25 13:51:44 UTC  
> Url: https://github.com/boostorg/json/pull/547#issuecomment-826326577  

# [Codecov](https://codecov.io/gh/boostorg/json/pull/547?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#547](https://codecov.io/gh/boostorg/json/pull/547?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (b72c7f7) into [develop](https://codecov.io/gh/boostorg/json/commit/9403530b598b271be004597a1d6bb7755ecf09dd?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (9403530) will **not change** coverage.  
> The diff coverage is `100.00%`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/json/pull/547/graphs/tree.svg?width=650&height=150&src=pr&token=HNiMmIjyKi&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://codecov.io/gh/boostorg/json/pull/547?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@           Coverage Diff            @@  
##           develop     #547   +/-   ##  
========================================  
  Coverage    99.10%   99.10%             
========================================  
  Files           67       67             
  Lines         6049     6049             
========================================  
  Hits          5995     5995             
  Misses          54       54             
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/json/pull/547?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/json/impl/object.hpp](https://codecov.io/gh/boostorg/json/pull/547/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9qc29uL2ltcGwvb2JqZWN0LmhwcA==) | `100.00% <100.00%> (ø)` | |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/json/pull/547?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/json/pull/547?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [9403530...b72c7f7](https://codecov.io/gh/boostorg/json/pull/547?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).

---

## Review 2 [Commented]

> Username: grisumbras  
> Created_at: 2021-04-25 13:49:43 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/547#pullrequestreview-644142352  

📁 test/object.cpp

```diff
1512 |+             o.reserve(1);
1513 |+         }
1514 |+         BOOST_TEST(res.allocated == res.deallocated);
```

> Username: grisumbras  
> Created_at: 2021-04-25 13:49:43 UTC  
> Updated_at: 2021-04-25 16:51:46 UTC  
> Url: https://github.com/boostorg/json/pull/547#discussion_r619819417  

@glenfe can you comment on whether this is a correct way to test that the same amount which was allocated was then deallocated?

> Username: glenfe  
> Created_at: 2021-04-25 15:51:31 UTC  
> Updated_at: 2021-04-25 16:51:46 UTC  
> Url: https://github.com/boostorg/json/pull/547#discussion_r619836428  

@grisumbras Yes, it is.


---

## Review 3 [Commented]

> Username: grisumbras  
> Created_at: 2021-04-25 13:50:48 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/547#pullrequestreview-644142464  

📁 CHANGELOG.md

```diff
   1 |+ Boost 1.77.0:
   2 |+ 
   3 |+ * Fixed memory deallocation bug in `object`.
```

> Username: grisumbras  
> Created_at: 2021-04-25 13:50:48 UTC  
> Updated_at: 2021-04-25 16:51:46 UTC  
> Url: https://github.com/boostorg/json/pull/547#discussion_r619819554  

@vinniefalco are you satisfied with this changelog entry? Also, see release notes entry below.


---

## Comment 4

> Username: cppalliance-bot  
> Created_at: 2021-04-25 14:39:00 UTC  
> Url: https://github.com/boostorg/json/pull/547#issuecomment-826335199  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/547/pullrequest-condensed-9e765de.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/547/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/547/pullrequest.html)

---
