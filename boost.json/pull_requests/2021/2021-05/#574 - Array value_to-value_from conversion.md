# #574 Array value_to/value_from conversion [Merged]

> Username: grisumbras  
> Created at: 2021-05-22 21:00:09 UTC  
> Updated at: 2021-05-25 14:08:54 UTC  
> Merged at: 2021-05-25 14:08:40 UTC  
> Closed at: 2021-05-25 14:08:40 UTC  
> Url: https://github.com/boostorg/json/pull/574  



---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2021-05-22 21:51:39 UTC  
> Updated_at: 2021-05-23 19:49:16 UTC  
> Url: https://github.com/boostorg/json/pull/574#issuecomment-846467919  

# [Codecov](https://codecov.io/gh/boostorg/json/pull/574?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#574](https://codecov.io/gh/boostorg/json/pull/574?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (c1cc972) into [develop](https://codecov.io/gh/boostorg/json/commit/e62a95d6379f2cbeaee18f997e68bd5c90db0ef1?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (e62a95d) will **increase** coverage by `0.00%`.  
> The diff coverage is `100.00%`.  
  
> :exclamation: Current head c1cc972 differs from pull request most recent head 351603c. Consider uploading reports for the commit 351603c to get more accurate results  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/json/pull/574/graphs/tree.svg?width=650&height=150&src=pr&token=HNiMmIjyKi&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://codecov.io/gh/boostorg/json/pull/574?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@           Coverage Diff            @@  
##           develop     #574   +/-   ##  
========================================  
  Coverage    99.05%   99.05%             
========================================  
  Files           68       68             
  Lines         6110     6116    +6       
========================================  
+ Hits          6052     6058    +6       
  Misses          58       58             
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/json/pull/574?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/json/detail/value\_traits.hpp](https://codecov.io/gh/boostorg/json/pull/574/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9qc29uL2RldGFpbC92YWx1ZV90cmFpdHMuaHBw) | `100.00% <ø> (ø)` | |  
| [include/boost/json/detail/value\_from.hpp](https://codecov.io/gh/boostorg/json/pull/574/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9qc29uL2RldGFpbC92YWx1ZV9mcm9tLmhwcA==) | `100.00% <100.00%> (ø)` | |  
| [include/boost/json/detail/value\_to.hpp](https://codecov.io/gh/boostorg/json/pull/574/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9qc29uL2RldGFpbC92YWx1ZV90by5ocHA=) | `100.00% <100.00%> (ø)` | |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/json/pull/574?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/json/pull/574?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [e62a95d...351603c](https://codecov.io/gh/boostorg/json/pull/574?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).

---

## Comment 2

> Username: cppalliance-bot  
> Created_at: 2021-05-22 22:54:01 UTC  
> Url: https://github.com/boostorg/json/pull/574#issuecomment-846473113  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/574/pullrequest-condensed-ee0bd53.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/574/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/574/pullrequest.html)

---

## Review 3 [Commented]

> Username: vinniefalco  
> Created_at: 2021-05-22 23:08:35 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/574#pullrequestreview-666216309  

📁 test/value_from.cpp

```diff
 217 |+             value c = value_from(a);
 218 |+             BOOST_TEST(c.is_array());
 219 |+             BOOST_TEST(serialize(c) == serialize(b));
```

> Username: vinniefalco  
> Created_at: 2021-05-22 23:08:35 UTC  
> Updated_at: 2021-05-22 23:08:36 UTC  
> Url: https://github.com/boostorg/json/pull/574#discussion_r637460772  

I think you can use `BOOST_TEST( c == b )` now (?)


---

## Comment 4

> Username: cppalliance-bot  
> Created_at: 2021-05-23 08:19:00 UTC  
> Url: https://github.com/boostorg/json/pull/574#issuecomment-846524437  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/574/pullrequest-condensed-8a2078d.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/574/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/574/pullrequest.html)

---

## Review 5 [Commented]

> Username: vinniefalco  
> Created_at: 2021-05-23 19:20:42 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/574#pullrequestreview-666297688  

📁 include/boost/json/detail/value_to.hpp

```diff
  69 |+     std::size_t,
  70 |+     priority_tag<0>)
  71 |+ { }
```

> Username: vinniefalco  
> Created_at: 2021-05-23 19:20:42 UTC  
> Url: https://github.com/boostorg/json/pull/574#discussion_r637589854  

Please put the braces on separate lines this helps coverage


---

## Comment 6

> Username: cppalliance-bot  
> Created_at: 2021-05-23 21:19:52 UTC  
> Url: https://github.com/boostorg/json/pull/574#issuecomment-846626000  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/574/pullrequest-condensed-3168005.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/574/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/574/pullrequest.html)

---
