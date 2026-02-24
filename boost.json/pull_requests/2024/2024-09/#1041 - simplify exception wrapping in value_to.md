# #1041 simplify exception wrapping in value_to [Merged]

> Username: grisumbras  
> Created at: 2024-09-16 19:07:35 UTC  
> Updated at: 2024-10-23 15:36:28 UTC  
> Merged at: 2024-10-23 12:41:18 UTC  
> Closed at: 2024-10-23 12:41:18 UTC  
> Url: https://github.com/boostorg/json/pull/1041  



---

## Comment 1

> Username: cppalliance-bot  
> Created_at: 2024-09-16 19:17:30 UTC  
> Url: https://github.com/boostorg/json/pull/1041#issuecomment-2353723509  

An automated preview of the documentation is available at [https://1041.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html](https://1041.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html)

---

## Comment 2

> Username: codecov[bot]  
> Created_at: 2024-09-16 19:56:25 UTC  
> Updated_at: 2024-10-23 15:36:28 UTC  
> Url: https://github.com/boostorg/json/pull/1041#issuecomment-2353796148  

## [Codecov](https://app.codecov.io/gh/boostorg/json/pull/1041?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
All modified and coverable lines are covered by tests :white_check_mark:  
> Project coverage is 93.40%. Comparing base [(`b6c916f`)](https://app.codecov.io/gh/boostorg/json/commit/b6c916f3a6f909d10611710f012e2be5cc733ecb?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) to head [(`6071cd5`)](https://app.codecov.io/gh/boostorg/json/commit/6071cd5808369462741218a0037e99f5b204c38c?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> Report is 1 commits behind head on develop.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/json/pull/1041/graphs/tree.svg?width=650&height=150&src=pr&token=HNiMmIjyKi&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/json/pull/1041?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@             Coverage Diff             @@  
##           develop    #1041      +/-   ##  
===========================================  
- Coverage    93.41%   93.40%   -0.01%       
===========================================  
  Files           91       91                
  Lines         8667     8658       -9       
===========================================  
- Hits          8096     8087       -9       
  Misses         571      571                
```  
  
| [Files with missing lines](https://app.codecov.io/gh/boostorg/json/pull/1041?dropdown=coverage&src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/json/detail/value\_to.hpp](https://app.codecov.io/gh/boostorg/json/pull/1041?src=pr&el=tree&filepath=include%2Fboost%2Fjson%2Fdetail%2Fvalue_to.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9qc29uL2RldGFpbC92YWx1ZV90by5ocHA=) | `100.00% <100.00%> (ø)` | |  
| [include/boost/json/impl/conversion.hpp](https://app.codecov.io/gh/boostorg/json/pull/1041?src=pr&el=tree&filepath=include%2Fboost%2Fjson%2Fimpl%2Fconversion.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9qc29uL2ltcGwvY29udmVyc2lvbi5ocHA=) | `100.00% <ø> (ø)` | |  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/json/pull/1041?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/json/pull/1041?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [b6c916f...6071cd5](https://app.codecov.io/gh/boostorg/json/pull/1041?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---

## Comment 3

> Username: cppalliance-bot  
> Created_at: 2024-09-16 20:14:57 UTC  
> Url: https://github.com/boostorg/json/pull/1041#issuecomment-2353858403  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/1041/pullrequest-condensed-cbff3f3.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/1041/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/1041/pullrequest.html)

---

## Comment 4

> Username: cppalliance-bot  
> Created_at: 2024-09-18 11:27:27 UTC  
> Url: https://github.com/boostorg/json/pull/1041#issuecomment-2358213709  

An automated preview of the documentation is available at [https://1041.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html](https://1041.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html)

---

## Comment 5

> Username: cppalliance-bot  
> Created_at: 2024-09-18 12:25:06 UTC  
> Url: https://github.com/boostorg/json/pull/1041#issuecomment-2358330191  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/1041/pullrequest-condensed-aabddbb.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/1041/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/1041/pullrequest.html)

---

## Comment 6

> Username: cppalliance-bot  
> Created_at: 2024-10-22 13:37:46 UTC  
> Url: https://github.com/boostorg/json/pull/1041#issuecomment-2429315106  

An automated preview of the documentation is available at [https://1041.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html](https://1041.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html)

---

## Comment 7

> Username: cppalliance-bot  
> Created_at: 2024-10-22 16:49:57 UTC  
> Url: https://github.com/boostorg/json/pull/1041#issuecomment-2429782442  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/1041/pullrequest-condensed-84b778c.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/1041/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/1041/pullrequest.html)

---
