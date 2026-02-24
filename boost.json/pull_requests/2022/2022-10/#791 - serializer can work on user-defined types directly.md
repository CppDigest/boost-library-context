# #791 serializer can work on user-defined types directly [Closed]

> Username: vinniefalco  
> Created at: 2022-10-27 02:39:30 UTC  
> Updated at: 2024-10-30 12:32:01 UTC  
> Closed at: 2024-10-30 12:32:01 UTC  
> Url: https://github.com/boostorg/json/pull/791  

This is just an experimental draft

---

## Comment 1

> Username: cppalliance-bot  
> Created_at: 2022-10-27 03:54:45 UTC  
> Url: https://github.com/boostorg/json/pull/791#issuecomment-1292936817  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/791/pullrequest-condensed-0eefc4f.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/791/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/791/pullrequest.html)

---

## Comment 2

> Username: cppalliance-bot  
> Created_at: 2022-10-27 07:14:56 UTC  
> Url: https://github.com/boostorg/json/pull/791#issuecomment-1293094502  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/791/pullrequest-condensed-8e94d11.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/791/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/791/pullrequest.html)

---

## Comment 3

> Username: codecov[bot]  
> Created_at: 2022-10-27 20:13:49 UTC  
> Updated_at: 2023-01-27 16:52:33 UTC  
> Url: https://github.com/boostorg/json/pull/791#issuecomment-1294015010  

# [Codecov](https://codecov.io/gh/boostorg/json/pull/791?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#791](https://codecov.io/gh/boostorg/json/pull/791?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (3433631) into [develop](https://codecov.io/gh/boostorg/json/commit/d7b7bef7057f3193f43d5390d707a3384c43a8a2?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (d7b7bef) will **decrease** coverage by `0.18%`.  
> The diff coverage is `94.57%`.  
  
> :exclamation: Current head 3433631 differs from pull request most recent head 9045c8a. Consider uploading reports for the commit 9045c8a to get more accurate results  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/json/pull/791/graphs/tree.svg?width=650&height=150&src=pr&token=HNiMmIjyKi&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://codecov.io/gh/boostorg/json/pull/791?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #791      +/-   ##  
===========================================  
- Coverage    99.00%   98.82%   -0.18%       
===========================================  
  Files           70       73       +3       
  Lines         6837     6655     -182       
===========================================  
- Hits          6769     6577     -192       
- Misses          68       78      +10       
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/json/pull/791?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/json/detail/stack.hpp](https://codecov.io/gh/boostorg/json/pull/791?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9qc29uL2RldGFpbC9zdGFjay5ocHA=) | `100.00% <ø> (ø)` | |  
| [include/boost/json/detail/stream.hpp](https://codecov.io/gh/boostorg/json/pull/791?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9qc29uL2RldGFpbC9zdHJlYW0uaHBw) | `100.00% <ø> (ø)` | |  
| [include/boost/json/serializer.hpp](https://codecov.io/gh/boostorg/json/pull/791?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9qc29uL3NlcmlhbGl6ZXIuaHBw) | `100.00% <ø> (ø)` | |  
| [include/boost/json/impl/serializer.ipp](https://codecov.io/gh/boostorg/json/pull/791?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9qc29uL2ltcGwvc2VyaWFsaXplci5pcHA=) | `92.61% <93.33%> (-4.38%)` | :arrow_down: |  
| [include/boost/json/detail/impl/writer.ipp](https://codecov.io/gh/boostorg/json/pull/791?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9qc29uL2RldGFpbC9pbXBsL3dyaXRlci5pcHA=) | `94.32% <94.32%> (ø)` | |  
| [include/boost/json/detail/impl/format.ipp](https://codecov.io/gh/boostorg/json/pull/791?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9qc29uL2RldGFpbC9pbXBsL2Zvcm1hdC5pcHA=) | `100.00% <100.00%> (ø)` | |  
| [include/boost/json/detail/writer.hpp](https://codecov.io/gh/boostorg/json/pull/791?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9qc29uL2RldGFpbC93cml0ZXIuaHBw) | `100.00% <100.00%> (ø)` | |  
| [include/boost/json/array.hpp](https://codecov.io/gh/boostorg/json/pull/791?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9qc29uL2FycmF5LmhwcA==) | `100.00% <0.00%> (ø)` | |  
| [include/boost/json/object.hpp](https://codecov.io/gh/boostorg/json/pull/791?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9qc29uL29iamVjdC5ocHA=) | `100.00% <0.00%> (ø)` | |  
| ... and [67 more](https://codecov.io/gh/boostorg/json/pull/791?src=pr&el=tree-more&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/json/pull/791?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/json/pull/791?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [d7b7bef...9045c8a](https://codecov.io/gh/boostorg/json/pull/791?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---

## Comment 4

> Username: cppalliance-bot  
> Created_at: 2022-10-27 22:50:43 UTC  
> Url: https://github.com/boostorg/json/pull/791#issuecomment-1294207714  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/791/pullrequest-condensed-a5634a5.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/791/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/791/pullrequest.html)

---

## Comment 5

> Username: cppalliance-bot  
> Created_at: 2022-10-28 00:39:46 UTC  
> Url: https://github.com/boostorg/json/pull/791#issuecomment-1294269971  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/791/pullrequest-condensed-efa6834.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/791/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/791/pullrequest.html)

---

## Review 6 [Commented]

> Username: vinniefalco  
> Created_at: 2022-12-14 02:35:56 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/791#pullrequestreview-1216697325  

📁 include/boost/json/detail/impl/writer.ipp

```diff
  17 |+ #include <boost/static_assert.hpp>
  18 |+ 
  19 |+ namespace boost {
```

> Username: vinniefalco  
> Created_at: 2022-12-14 02:35:56 UTC  
> Url: https://github.com/boostorg/json/pull/791#discussion_r1047951838  

we should probably get rid of this file :)


---

## Comment 7

> Username: cppalliance-bot  
> Created_at: 2022-12-29 04:31:27 UTC  
> Url: https://github.com/boostorg/json/pull/791#issuecomment-1367066351  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/791/pullrequest-condensed-ce86234.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/791/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/791/pullrequest.html)

---

## Comment 8

> Username: cppalliance-bot  
> Created_at: 2023-01-27 19:15:40 UTC  
> Url: https://github.com/boostorg/json/pull/791#issuecomment-1406966253  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/791/pullrequest-condensed-fdc10d8.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/791/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/791/pullrequest.html)

---
