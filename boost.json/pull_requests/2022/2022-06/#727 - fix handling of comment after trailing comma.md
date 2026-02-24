# #727 fix handling of comment after trailing comma [Merged]

> Username: grisumbras  
> Created at: 2022-06-30 15:08:29 UTC  
> Updated at: 2022-06-30 16:24:50 UTC  
> Merged at: 2022-06-30 15:49:55 UTC  
> Closed at: 2022-06-30 15:49:56 UTC  
> Url: https://github.com/boostorg/json/pull/727  

Fix #726

---

## Review 1 [Commented]

> Username: vinniefalco  
> Created_at: 2022-06-30 15:09:26 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/727#pullrequestreview-1025047142  

📁 include/boost/json/basic_parser_impl.hpp

```diff
1895 |     if(BOOST_JSON_LIKELY(*cs != ']'))
1896 |     {
1897 |+ loop:
```

> Username: vinniefalco  
> Created_at: 2022-06-30 15:09:25 UTC  
> Updated_at: 2022-06-30 15:09:26 UTC  
> Url: https://github.com/boostorg/json/pull/727#discussion_r911145364  

nice


---

## Comment 2

> Username: codecov[bot]  
> Created_at: 2022-06-30 15:23:15 UTC  
> Updated_at: 2022-06-30 15:24:08 UTC  
> Url: https://github.com/boostorg/json/pull/727#issuecomment-1171356343  

# [Codecov](https://codecov.io/gh/boostorg/json/pull/727?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#727](https://codecov.io/gh/boostorg/json/pull/727?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (75367a8) into [develop](https://codecov.io/gh/boostorg/json/commit/5874418faa12490eff3e51cb84fb197ca0f898bc?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (5874418) will **not change** coverage.  
> The diff coverage is `100.00%`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/json/pull/727/graphs/tree.svg?width=650&height=150&src=pr&token=HNiMmIjyKi&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://codecov.io/gh/boostorg/json/pull/727?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@           Coverage Diff            @@  
##           develop     #727   +/-   ##  
========================================  
  Coverage    99.13%   99.13%             
========================================  
  Files           69       69             
  Lines         6553     6553             
========================================  
  Hits          6496     6496             
  Misses          57       57             
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/json/pull/727?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/json/basic\_parser\_impl.hpp](https://codecov.io/gh/boostorg/json/pull/727/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9qc29uL2Jhc2ljX3BhcnNlcl9pbXBsLmhwcA==) | `99.20% <100.00%> (ø)` | |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/json/pull/727?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/json/pull/727?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [5874418...75367a8](https://codecov.io/gh/boostorg/json/pull/727?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).

---

## Comment 3

> Username: cppalliance-bot  
> Created_at: 2022-06-30 16:24:50 UTC  
> Url: https://github.com/boostorg/json/pull/727#issuecomment-1171425528  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/727/pullrequest-condensed-56ef1ea.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/727/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/727/pullrequest.html)

---
