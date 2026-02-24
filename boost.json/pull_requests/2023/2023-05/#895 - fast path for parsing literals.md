# #895 fast path for parsing literals [Merged]

> Username: grisumbras  
> Created at: 2023-05-26 09:45:16 UTC  
> Updated at: 2023-05-27 07:15:35 UTC  
> Merged at: 2023-05-27 06:03:59 UTC  
> Closed at: 2023-05-27 06:03:59 UTC  
> Url: https://github.com/boostorg/json/pull/895  



---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2023-05-26 10:03:46 UTC  
> Updated_at: 2023-05-27 05:58:33 UTC  
> Url: https://github.com/boostorg/json/pull/895#issuecomment-1564140622  

## [Codecov](https://app.codecov.io/gh/boostorg/json/pull/895?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#895](https://app.codecov.io/gh/boostorg/json/pull/895?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (d54473d) into [develop](https://app.codecov.io/gh/boostorg/json/commit/a6126253ea0486340b999530ab46e6dd5ccd11c0?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (a612625) will **decrease** coverage by `0.02%`.  
> The diff coverage is `94.11%`.  
  
> :exclamation: Current head d54473d differs from pull request most recent head ac9080c. Consider uploading reports for the commit ac9080c to get more accurate results  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/json/pull/895/graphs/tree.svg?width=650&height=150&src=pr&token=HNiMmIjyKi&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/json/pull/895?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #895      +/-   ##  
===========================================  
- Coverage    99.25%   99.24%   -0.02%       
===========================================  
  Files           71       71                
  Lines         6844     6861      +17       
===========================================  
+ Hits          6793     6809      +16       
- Misses          51       52       +1       
```  
  
  
| [Impacted Files](https://app.codecov.io/gh/boostorg/json/pull/895?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/json/basic\_parser\_impl.hpp](https://app.codecov.io/gh/boostorg/json/pull/895?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9qc29uL2Jhc2ljX3BhcnNlcl9pbXBsLmhwcA==) | `98.53% <94.11%> (-0.07%)` | :arrow_down: |  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/json/pull/895?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/json/pull/895?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [a612625...ac9080c](https://app.codecov.io/gh/boostorg/json/pull/895?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---

## Comment 2

> Username: cppalliance-bot  
> Created_at: 2023-05-26 11:00:41 UTC  
> Url: https://github.com/boostorg/json/pull/895#issuecomment-1564217607  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/895/pullrequest-condensed-c229e0a.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/895/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/895/pullrequest.html)

---

## Comment 3

> Username: vinniefalco  
> Created_at: 2023-05-26 21:28:24 UTC  
> Url: https://github.com/boostorg/json/pull/895#issuecomment-1564978209  

seems OK I guess

---

## Comment 4

> Username: cppalliance-bot  
> Created_at: 2023-05-27 07:15:34 UTC  
> Url: https://github.com/boostorg/json/pull/895#issuecomment-1565247648  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/895/pullrequest-condensed-2e7e165.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/895/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/895/pullrequest.html)

---
