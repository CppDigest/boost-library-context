# #568 Do not crash in array constructor from iterators that form an empty range [Merged]

> Username: grisumbras  
> Created at: 2021-05-07 20:55:54 UTC  
> Updated at: 2021-05-22 12:47:11 UTC  
> Merged at: 2021-05-22 12:46:56 UTC  
> Closed at: 2021-05-22 12:46:56 UTC  
> Url: https://github.com/boostorg/json/pull/568  

Fix #567

---

## Comment 1

> Username: cppalliance-bot  
> Created_at: 2021-05-07 21:01:27 UTC  
> Url: https://github.com/boostorg/json/pull/568#issuecomment-834777068  

An automated preview of the documentation is available at [https://568.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html](https://568.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html)

---

## Comment 2

> Username: codecov[bot]  
> Created_at: 2021-05-07 21:15:20 UTC  
> Updated_at: 2021-05-08 12:47:23 UTC  
> Url: https://github.com/boostorg/json/pull/568#issuecomment-834785102  

# [Codecov](https://codecov.io/gh/boostorg/json/pull/568?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#568](https://codecov.io/gh/boostorg/json/pull/568?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (112f2af) into [develop](https://codecov.io/gh/boostorg/json/commit/2ebbd0cfe3e8186b2416ea0c3cb898a93d4e1f9c?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (2ebbd0c) will **decrease** coverage by `0.06%`.  
> The diff coverage is `100.00%`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/json/pull/568/graphs/tree.svg?width=650&height=150&src=pr&token=HNiMmIjyKi&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://codecov.io/gh/boostorg/json/pull/568?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #568      +/-   ##  
===========================================  
- Coverage    99.11%   99.05%   -0.07%       
===========================================  
  Files           68       68                
  Lines         6110     6117       +7       
===========================================  
+ Hits          6056     6059       +3       
- Misses          54       58       +4       
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/json/pull/568?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/json/impl/array.hpp](https://codecov.io/gh/boostorg/json/pull/568/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9qc29uL2ltcGwvYXJyYXkuaHBw) | `100.00% <100.00%> (ø)` | |  
| [include/boost/json/value.hpp](https://codecov.io/gh/boostorg/json/pull/568/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9qc29uL3ZhbHVlLmhwcA==) | `98.98% <0.00%> (-0.81%)` | :arrow_down: |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/json/pull/568?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/json/pull/568?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [2ebbd0c...112f2af](https://codecov.io/gh/boostorg/json/pull/568?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).

---

## Comment 3

> Username: pdimov  
> Created_at: 2021-05-07 21:18:26 UTC  
> Url: https://github.com/boostorg/json/pull/568#issuecomment-834786961  

I'd suggest adding tests that use real containers such as `vector` and `list`.

---

## Comment 4

> Username: grisumbras  
> Created_at: 2021-05-07 21:29:00 UTC  
> Url: https://github.com/boostorg/json/pull/568#issuecomment-834792732  

What would be different in those tests?

---

## Comment 5

> Username: pdimov  
> Created_at: 2021-05-07 21:37:01 UTC  
> Url: https://github.com/boostorg/json/pull/568#issuecomment-834797002  

They would test random access and bidirectional iterators.

---

## Comment 6

> Username: cppalliance-bot  
> Created_at: 2021-05-08 00:25:01 UTC  
> Url: https://github.com/boostorg/json/pull/568#issuecomment-834896350  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/568/pullrequest-condensed-ad87788.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/568/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/568/pullrequest.html)

---

## Comment 7

> Username: cppalliance-bot  
> Created_at: 2021-05-08 13:44:04 UTC  
> Url: https://github.com/boostorg/json/pull/568#issuecomment-835369878  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/568/pullrequest-condensed-968c417.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/568/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/568/pullrequest.html)

---
