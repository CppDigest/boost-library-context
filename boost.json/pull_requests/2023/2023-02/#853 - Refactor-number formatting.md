# #853 Refactor/number formatting [Closed]

> Username: grisumbras  
> Created at: 2023-02-05 09:24:36 UTC  
> Updated at: 2025-01-09 08:45:13 UTC  
> Closed at: 2025-01-09 08:45:04 UTC  
> Url: https://github.com/boostorg/json/pull/853  



---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2023-02-05 09:53:06 UTC  
> Updated_at: 2025-01-09 08:45:13 UTC  
> Url: https://github.com/boostorg/json/pull/853#issuecomment-1417274030  

## [Codecov](https://app.codecov.io/gh/boostorg/json/pull/853?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
All modified and coverable lines are covered by tests :white_check_mark:  
> Project coverage is 99.19%. Comparing base [(`22c1b39`)](https://app.codecov.io/gh/boostorg/json/commit/22c1b391d4fe8b35a5c5637f8677d6d619a25e6e?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) to head [(`b7b6382`)](https://app.codecov.io/gh/boostorg/json/commit/b7b638291dce44fc727a4f30c1b0e0116ea8ac55?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> Report is 227 commits behind head on develop.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/json/pull/853/graphs/tree.svg?width=650&height=150&src=pr&token=HNiMmIjyKi&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/json/pull/853?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@           Coverage Diff            @@  
##           develop     #853   +/-   ##  
========================================  
  Coverage    99.19%   99.19%             
========================================  
  Files           70       70             
  Lines         6849     6859   +10       
========================================  
+ Hits          6794     6804   +10       
  Misses          55       55             
```  
  
| [Files with missing lines](https://app.codecov.io/gh/boostorg/json/pull/853?dropdown=coverage&src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/json/detail/impl/format.ipp](https://app.codecov.io/gh/boostorg/json/pull/853?src=pr&el=tree&filepath=include%2Fboost%2Fjson%2Fdetail%2Fimpl%2Fformat.ipp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9qc29uL2RldGFpbC9pbXBsL2Zvcm1hdC5pcHA=) | `100.00% <100.00%> (ø)` | |  
| [include/boost/json/detail/stream.hpp](https://app.codecov.io/gh/boostorg/json/pull/853?src=pr&el=tree&filepath=include%2Fboost%2Fjson%2Fdetail%2Fstream.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9qc29uL2RldGFpbC9zdHJlYW0uaHBw) | `100.00% <100.00%> (ø)` | |  
| [include/boost/json/impl/serializer.ipp](https://app.codecov.io/gh/boostorg/json/pull/853?src=pr&el=tree&filepath=include%2Fboost%2Fjson%2Fimpl%2Fserializer.ipp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9qc29uL2ltcGwvc2VyaWFsaXplci5pcHA=) | `98.62% <100.00%> (ø)` | |  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/json/pull/853?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/json/pull/853?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [22c1b39...b7b6382](https://app.codecov.io/gh/boostorg/json/pull/853?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---

## Comment 2

> Username: cppalliance-bot  
> Created_at: 2023-02-05 10:49:43 UTC  
> Url: https://github.com/boostorg/json/pull/853#issuecomment-1417427391  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/853/pullrequest-condensed-4873287.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/853/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/853/pullrequest.html)

---
