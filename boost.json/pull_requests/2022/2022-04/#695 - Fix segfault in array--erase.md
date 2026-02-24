# #695 Fix segfault in array::erase [Merged]

> Username: grisumbras  
> Created at: 2022-04-20 11:54:52 UTC  
> Updated at: 2022-05-14 15:45:26 UTC  
> Merged at: 2022-05-14 15:44:59 UTC  
> Closed at: 2022-05-14 15:44:59 UTC  
> Url: https://github.com/boostorg/json/pull/695  

Fix #692

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2022-04-20 12:07:54 UTC  
> Updated_at: 2022-05-14 12:40:07 UTC  
> Url: https://github.com/boostorg/json/pull/695#issuecomment-1103856266  

# [Codecov](https://codecov.io/gh/boostorg/json/pull/695?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#695](https://codecov.io/gh/boostorg/json/pull/695?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (c9ab3fa) into [develop](https://codecov.io/gh/boostorg/json/commit/8be6f6bbb6a299cf22216c1e5643ff586e1ba3f5?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (8be6f6b) will **decrease** coverage by `0.00%`.  
> The diff coverage is `100.00%`.  
  
> :exclamation: Current head c9ab3fa differs from pull request most recent head 07e3ef1. Consider uploading reports for the commit 07e3ef1 to get more accurate results  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/json/pull/695/graphs/tree.svg?width=650&height=150&src=pr&token=HNiMmIjyKi&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://codecov.io/gh/boostorg/json/pull/695?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #695      +/-   ##  
===========================================  
- Coverage    99.08%   99.08%   -0.01%       
===========================================  
  Files           69       69                
  Lines         6546     6542       -4       
===========================================  
- Hits          6486     6482       -4       
  Misses          60       60                
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/json/pull/695?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/json/impl/array.ipp](https://codecov.io/gh/boostorg/json/pull/695/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9qc29uL2ltcGwvYXJyYXkuaXBw) | `100.00% <100.00%> (ø)` | |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/json/pull/695?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/json/pull/695?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [8be6f6b...07e3ef1](https://codecov.io/gh/boostorg/json/pull/695?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).

---

## Comment 2

> Username: cppalliance-bot  
> Created_at: 2022-04-20 13:09:34 UTC  
> Url: https://github.com/boostorg/json/pull/695#issuecomment-1103913553  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/695/pullrequest-condensed-62a170c.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/695/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/695/pullrequest.html)

---

## Comment 3

> Username: vinniefalco  
> Created_at: 2022-04-20 13:15:06 UTC  
> Url: https://github.com/boostorg/json/pull/695#issuecomment-1103919193  

well yeah that's another way to do it :)   
  
Just squash this down and keep your name on it please... we dont need 2 commits for this.

---

## Comment 4

> Username: cppalliance-bot  
> Created_at: 2022-05-14 13:54:37 UTC  
> Url: https://github.com/boostorg/json/pull/695#issuecomment-1126720520  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/695/pullrequest-condensed-39117fb.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/695/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/695/pullrequest.html)

---
