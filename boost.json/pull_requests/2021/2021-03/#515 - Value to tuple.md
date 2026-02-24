# #515 Value to tuple [Merged]

> Username: grisumbras  
> Created at: 2021-03-03 07:18:16 UTC  
> Updated at: 2021-04-26 05:48:27 UTC  
> Merged at: 2021-04-26 05:47:56 UTC  
> Closed at: 2021-04-26 05:47:56 UTC  
> Url: https://github.com/boostorg/json/pull/515  

Fix #508

---

## Comment 1

> Username: cppalliance-bot  
> Created_at: 2021-03-03 08:33:46 UTC  
> Url: https://github.com/boostorg/json/pull/515#issuecomment-789538022  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/515/pullrequest-condensed-15fcd84.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/515/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/515/pullrequest.html)

---

## Comment 2

> Username: codecov[bot]  
> Created_at: 2021-03-03 18:53:48 UTC  
> Updated_at: 2021-04-26 00:35:20 UTC  
> Url: https://github.com/boostorg/json/pull/515#issuecomment-789974397  

# [Codecov](https://codecov.io/gh/boostorg/json/pull/515?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#515](https://codecov.io/gh/boostorg/json/pull/515?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (00d620c) into [develop](https://codecov.io/gh/boostorg/json/commit/bd5c548beb55e9060d12740a361b8af20d35dc59?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (bd5c548) will **increase** coverage by `0.00%`.  
> The diff coverage is `100.00%`.  
  
> :exclamation: Current head 00d620c differs from pull request most recent head aae1863. Consider uploading reports for the commit aae1863 to get more accurate results  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/json/pull/515/graphs/tree.svg?width=650&height=150&src=pr&token=HNiMmIjyKi&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://codecov.io/gh/boostorg/json/pull/515?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@           Coverage Diff            @@  
##           develop     #515   +/-   ##  
========================================  
  Coverage    99.10%   99.10%             
========================================  
  Files           67       67             
  Lines         6049     6057    +8       
========================================  
+ Hits          5995     6003    +8       
  Misses          54       54             
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/json/pull/515?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/json/detail/value\_traits.hpp](https://codecov.io/gh/boostorg/json/pull/515/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9qc29uL2RldGFpbC92YWx1ZV90cmFpdHMuaHBw) | `100.00% <ø> (ø)` | |  
| [include/boost/json/detail/value\_to.hpp](https://codecov.io/gh/boostorg/json/pull/515/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9qc29uL2RldGFpbC92YWx1ZV90by5ocHA=) | `100.00% <100.00%> (ø)` | |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/json/pull/515?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/json/pull/515?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [0d65b0a...aae1863](https://codecov.io/gh/boostorg/json/pull/515?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).

---

## Comment 3

> Username: cppalliance-bot  
> Created_at: 2021-03-03 20:48:59 UTC  
> Url: https://github.com/boostorg/json/pull/515#issuecomment-790044930  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/515/pullrequest-condensed-f7ba0f2.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/515/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/515/pullrequest.html)

---

## Review 4 [Commented]

> Username: vinniefalco  
> Created_at: 2021-03-03 23:26:16 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/515#pullrequestreview-603462654  

📁 include/boost/json/detail/index_sequence.hpp

```diff
  24 |+ 
  25 |+ template <std::size_t... Is>
  26 |+ using index_sequence = boost::mp11::index_sequence<Is...>;
```

> Username: vinniefalco  
> Created_at: 2021-03-03 23:26:16 UTC  
> Updated_at: 2021-04-26 00:35:15 UTC  
> Url: https://github.com/boostorg/json/pull/515#discussion_r586875525  

Very nice


---

## Comment 5

> Username: vinniefalco  
> Created_at: 2021-03-03 23:27:18 UTC  
> Url: https://github.com/boostorg/json/pull/515#issuecomment-790147806  

I really don't have the skills to review stuff related to `value_to` and `value_from`. @pdimov ?

---

## Comment 6

> Username: pdimov  
> Created_at: 2021-03-03 23:40:54 UTC  
> Url: https://github.com/boostorg/json/pull/515#issuecomment-790153907  

If it passes msvc-14.0 CI it's probably good.

---

## Comment 7

> Username: pdimov  
> Created_at: 2021-03-03 23:50:11 UTC  
> Url: https://github.com/boostorg/json/pull/515#issuecomment-790159269  

Looks good to me. To repeat what I said on Slack, now that tuple-likes are supported it looks like general maps (where the key is not string-like) would also start working (being treated as sequences of pairs.) If they do work, we need to have a test for it. If they don't, we might want to look into why.

---

## Comment 8

> Username: cppalliance-bot  
> Created_at: 2021-03-09 13:49:07 UTC  
> Url: https://github.com/boostorg/json/pull/515#issuecomment-793922601  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/515/pullrequest-condensed-8e71200.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/515/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/515/pullrequest.html)

---

## Comment 9

> Username: cppalliance-bot  
> Created_at: 2021-03-17 14:29:03 UTC  
> Url: https://github.com/boostorg/json/pull/515#issuecomment-801129968  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/515/pullrequest-condensed-5ca52e6.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/515/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/515/pullrequest.html)

---

## Comment 10

> Username: cppalliance-bot  
> Created_at: 2021-03-18 06:29:01 UTC  
> Url: https://github.com/boostorg/json/pull/515#issuecomment-801667189  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/515/pullrequest-condensed-59b2625.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/515/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/515/pullrequest.html)

---

## Comment 11

> Username: cppalliance-bot  
> Created_at: 2021-04-25 23:37:41 UTC  
> Url: https://github.com/boostorg/json/pull/515#issuecomment-826415023  

An automated preview of the documentation is available at [https://515.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html](https://515.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html)

---

## Comment 12

> Username: cppalliance-bot  
> Created_at: 2021-04-25 23:51:23 UTC  
> Url: https://github.com/boostorg/json/pull/515#issuecomment-826417056  

An automated preview of the documentation is available at [https://515.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html](https://515.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html)

---

## Comment 13

> Username: cppalliance-bot  
> Created_at: 2021-04-26 00:42:00 UTC  
> Url: https://github.com/boostorg/json/pull/515#issuecomment-826430229  

An automated preview of the documentation is available at [https://515.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html](https://515.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html)

---

## Comment 14

> Username: cppalliance-bot  
> Created_at: 2021-04-26 01:49:03 UTC  
> Url: https://github.com/boostorg/json/pull/515#issuecomment-826448562  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/515/pullrequest-condensed-d4ddd7b.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/515/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/515/pullrequest.html)

---
