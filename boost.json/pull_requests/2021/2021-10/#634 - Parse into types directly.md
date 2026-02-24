# #634 Parse into types directly [Closed]

> Username: grisumbras  
> Created at: 2021-10-05 15:22:04 UTC  
> Updated at: 2024-10-10 07:55:01 UTC  
> Closed at: 2023-10-26 16:55:10 UTC  
> Url: https://github.com/boostorg/json/pull/634  



---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2021-10-05 15:57:11 UTC  
> Updated_at: 2021-11-05 18:37:47 UTC  
> Url: https://github.com/boostorg/json/pull/634#issuecomment-934539716  

# [Codecov](https://codecov.io/gh/boostorg/json/pull/634?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#634](https://codecov.io/gh/boostorg/json/pull/634?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (b15d0c6) into [develop](https://codecov.io/gh/boostorg/json/commit/9af467c41631d5b4bf3810a154b7d838bac2fef4?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (9af467c) will **decrease** coverage by `1.33%`.  
> The diff coverage is `62.66%`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/json/pull/634/graphs/tree.svg?width=650&height=150&src=pr&token=HNiMmIjyKi&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://codecov.io/gh/boostorg/json/pull/634?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #634      +/-   ##  
===========================================  
- Coverage    99.05%   97.71%   -1.34%       
===========================================  
  Files           68       70       +2       
  Lines         6119     6357     +238       
===========================================  
+ Hits          6061     6212     +151       
- Misses          58      145      +87       
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/json/pull/634?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/json/detail/parse\_into.hpp](https://codecov.io/gh/boostorg/json/pull/634/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9qc29uL2RldGFpbC9wYXJzZV9pbnRvLmhwcA==) | `60.36% <60.36%> (ø)` | |  
| [include/boost/json/impl/parse\_into.hpp](https://codecov.io/gh/boostorg/json/pull/634/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9qc29uL2ltcGwvcGFyc2VfaW50by5ocHA=) | `80.00% <80.00%> (ø)` | |  
| [include/boost/json/impl/error.ipp](https://codecov.io/gh/boostorg/json/pull/634/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9qc29uL2ltcGwvZXJyb3IuaXBw) | `100.00% <100.00%> (ø)` | |  
| [include/boost/json/value\_to.hpp](https://codecov.io/gh/boostorg/json/pull/634/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9qc29uL3ZhbHVlX3RvLmhwcA==) | `100.00% <0.00%> (ø)` | |  
| [include/boost/json/value\_from.hpp](https://codecov.io/gh/boostorg/json/pull/634/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9qc29uL3ZhbHVlX2Zyb20uaHBw) | `100.00% <0.00%> (ø)` | |  
| [include/boost/json/detail/config.hpp](https://codecov.io/gh/boostorg/json/pull/634/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9qc29uL2RldGFpbC9jb25maWcuaHBw) | `100.00% <0.00%> (ø)` | |  
| [include/boost/json/detail/value\_from.hpp](https://codecov.io/gh/boostorg/json/pull/634/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9qc29uL2RldGFpbC92YWx1ZV9mcm9tLmhwcA==) | `100.00% <0.00%> (ø)` | |  
| [include/boost/json/basic\_parser\_impl.hpp](https://codecov.io/gh/boostorg/json/pull/634/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9qc29uL2Jhc2ljX3BhcnNlcl9pbXBsLmhwcA==) | `99.19% <0.00%> (+<0.01%)` | :arrow_up: |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/json/pull/634?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/json/pull/634?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [9af467c...b15d0c6](https://codecov.io/gh/boostorg/json/pull/634?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).

---

## Comment 2

> Username: cppalliance-bot  
> Created_at: 2021-10-05 16:33:43 UTC  
> Url: https://github.com/boostorg/json/pull/634#issuecomment-934571504  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/634/pullrequest-condensed-7a20a56.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/634/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/634/pullrequest.html)

---

## Comment 3

> Username: cppalliance-bot  
> Created_at: 2021-10-06 21:24:32 UTC  
> Url: https://github.com/boostorg/json/pull/634#issuecomment-937166594  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/634/pullrequest-condensed-88d7094.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/634/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/634/pullrequest.html)

---

## Comment 4

> Username: cppalliance-bot  
> Created_at: 2021-10-07 04:18:41 UTC  
> Url: https://github.com/boostorg/json/pull/634#issuecomment-937431888  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/634/pullrequest-condensed-4c1786a.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/634/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/634/pullrequest.html)

---

## Comment 5

> Username: cppalliance-bot  
> Created_at: 2021-10-07 07:03:37 UTC  
> Url: https://github.com/boostorg/json/pull/634#issuecomment-937512195  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/634/pullrequest-condensed-ad71013.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/634/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/634/pullrequest.html)

---

## Comment 6

> Username: cppalliance-bot  
> Created_at: 2021-10-07 09:38:38 UTC  
> Url: https://github.com/boostorg/json/pull/634#issuecomment-937624352  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/634/pullrequest-condensed-781293a.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/634/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/634/pullrequest.html)

---

## Comment 7

> Username: cppalliance-bot  
> Created_at: 2021-10-07 15:04:06 UTC  
> Url: https://github.com/boostorg/json/pull/634#issuecomment-937881240  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/634/pullrequest-condensed-034b924.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/634/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/634/pullrequest.html)

---

## Comment 8

> Username: cppalliance-bot  
> Created_at: 2021-10-08 02:44:57 UTC  
> Url: https://github.com/boostorg/json/pull/634#issuecomment-938294677  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/634/pullrequest-condensed-8ac9e58.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/634/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/634/pullrequest.html)

---

## Comment 9

> Username: madmongo1  
> Created_at: 2021-11-05 17:34:53 UTC  
> Url: https://github.com/boostorg/json/pull/634#issuecomment-962085918  

@grisumbras bump!  
  
This is a much-missed feature.

---

## Comment 10

> Username: cppalliance-bot  
> Created_at: 2021-11-05 18:59:09 UTC  
> Url: https://github.com/boostorg/json/pull/634#issuecomment-962140840  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/634/pullrequest-condensed-ba7bf27.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/634/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/634/pullrequest.html)

---

## Comment 11

> Username: grisumbras  
> Created_at: 2023-10-26 16:55:10 UTC  
> Url: https://github.com/boostorg/json/pull/634#issuecomment-1781495549  

I went with #924 instead.

---
