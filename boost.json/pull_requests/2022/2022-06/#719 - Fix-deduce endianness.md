# #719 Fix/deduce endianness [Merged]

> Username: grisumbras  
> Created at: 2022-06-09 16:00:57 UTC  
> Updated at: 2022-06-12 06:40:14 UTC  
> Merged at: 2022-06-12 06:40:10 UTC  
> Closed at: 2022-06-12 06:40:10 UTC  
> Url: https://github.com/boostorg/json/pull/719  

Fix #717   
Also see #576

---

## Comment 1

> Username: cppalliance-bot  
> Created_at: 2022-06-09 19:44:32 UTC  
> Url: https://github.com/boostorg/json/pull/719#issuecomment-1151551671  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/719/pullrequest-condensed-4537738.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/719/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/719/pullrequest.html)

---

## Comment 2

> Username: cppalliance-bot  
> Created_at: 2022-06-10 06:39:38 UTC  
> Url: https://github.com/boostorg/json/pull/719#issuecomment-1152017583  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/719/pullrequest-condensed-7e0aec7.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/719/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/719/pullrequest.html)

---

## Comment 3

> Username: cppalliance-bot  
> Created_at: 2022-06-10 11:59:36 UTC  
> Url: https://github.com/boostorg/json/pull/719#issuecomment-1152283944  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/719/pullrequest-condensed-e7cf488.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/719/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/719/pullrequest.html)

---

## Comment 4

> Username: codecov[bot]  
> Created_at: 2022-06-10 16:18:28 UTC  
> Updated_at: 2022-06-11 15:29:51 UTC  
> Url: https://github.com/boostorg/json/pull/719#issuecomment-1152527774  

# [Codecov](https://codecov.io/gh/boostorg/json/pull/719?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#719](https://codecov.io/gh/boostorg/json/pull/719?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (b87b777) into [develop](https://codecov.io/gh/boostorg/json/commit/79700a1f651b411864faa433e44eb73078164654?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (79700a1) will **increase** coverage by `0.04%`.  
> The diff coverage is `100.00%`.  
  
> :exclamation: Current head b87b777 differs from pull request most recent head f1b9420. Consider uploading reports for the commit f1b9420 to get more accurate results  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/json/pull/719/graphs/tree.svg?width=650&height=150&src=pr&token=HNiMmIjyKi&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://codecov.io/gh/boostorg/json/pull/719?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #719      +/-   ##  
===========================================  
+ Coverage    99.08%   99.13%   +0.04%       
===========================================  
  Files           69       69                
  Lines         6553     6552       -1       
===========================================  
+ Hits          6493     6495       +2       
+ Misses          60       57       -3       
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/json/pull/719?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/json/basic\_parser\_impl.hpp](https://codecov.io/gh/boostorg/json/pull/719/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9qc29uL2Jhc2ljX3BhcnNlcl9pbXBsLmhwcA==) | `99.20% <ø> (ø)` | |  
| [include/boost/json/detail/config.hpp](https://codecov.io/gh/boostorg/json/pull/719/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9qc29uL2RldGFpbC9jb25maWcuaHBw) | `100.00% <ø> (ø)` | |  
| [include/boost/json/detail/sse2.hpp](https://codecov.io/gh/boostorg/json/pull/719/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9qc29uL2RldGFpbC9zc2UyLmhwcA==) | `97.77% <100.00%> (-0.02%)` | :arrow_down: |  
| [include/boost/json/detail/utf8.hpp](https://codecov.io/gh/boostorg/json/pull/719/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9qc29uL2RldGFpbC91dGY4LmhwcA==) | `100.00% <100.00%> (+4.91%)` | :arrow_up: |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/json/pull/719?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/json/pull/719?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [79700a1...f1b9420](https://codecov.io/gh/boostorg/json/pull/719?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).

---

## Comment 5

> Username: cppalliance-bot  
> Created_at: 2022-06-10 18:24:36 UTC  
> Url: https://github.com/boostorg/json/pull/719#issuecomment-1152624457  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/719/pullrequest-condensed-0753a12.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/719/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/719/pullrequest.html)

---

## Comment 6

> Username: cppalliance-bot  
> Created_at: 2022-06-11 09:09:33 UTC  
> Url: https://github.com/boostorg/json/pull/719#issuecomment-1152886623  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/719/pullrequest-condensed-ee76fec.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/719/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/719/pullrequest.html)

---

## Comment 7

> Username: vinniefalco  
> Created_at: 2022-06-11 14:45:59 UTC  
> Url: https://github.com/boostorg/json/pull/719#issuecomment-1152941692  

Please no negative commit messages e.g. "confusing code." Instead express it as a positive "add clarifying implementation notes"

---

## Comment 8

> Username: cppalliance-bot  
> Created_at: 2022-06-11 16:44:36 UTC  
> Url: https://github.com/boostorg/json/pull/719#issuecomment-1152961987  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/719/pullrequest-condensed-9e9faac.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/719/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/719/pullrequest.html)

---
