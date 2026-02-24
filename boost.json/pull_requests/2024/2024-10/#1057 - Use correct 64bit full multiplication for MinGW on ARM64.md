# #1057 Use correct 64bit full multiplication for MinGW on ARM64 [Merged]

> Username: vejbomar  
> Created at: 2024-10-18 07:37:30 UTC  
> Updated at: 2024-10-22 14:52:50 UTC  
> Merged at: 2024-10-22 13:18:53 UTC  
> Closed at: 2024-10-22 13:18:53 UTC  
> Url: https://github.com/boostorg/json/pull/1057  

Update `fast_float` code from `charconv` to use correct 64bit full multiplication for MinGW on ARM64.  
  
PR that did the same in `charconv`: https://github.com/boostorg/charconv/pull/230

---

## Comment 1

> Username: cppalliance-bot  
> Created_at: 2024-10-18 07:47:25 UTC  
> Url: https://github.com/boostorg/json/pull/1057#issuecomment-2421692904  

An automated preview of the documentation is available at [https://1057.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html](https://1057.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html)

---

## Comment 2

> Username: codecov[bot]  
> Created_at: 2024-10-18 08:28:02 UTC  
> Updated_at: 2024-10-22 14:52:50 UTC  
> Url: https://github.com/boostorg/json/pull/1057#issuecomment-2421819559  

## [Codecov](https://app.codecov.io/gh/boostorg/json/pull/1057?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
All modified and coverable lines are covered by tests :white_check_mark:  
> Project coverage is 93.41%. Comparing base [(`5bdbb23`)](https://app.codecov.io/gh/boostorg/json/commit/5bdbb23e7cb68258959ac645ae196d2a62464418?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) to head [(`b6c916f`)](https://app.codecov.io/gh/boostorg/json/commit/b6c916f3a6f909d10611710f012e2be5cc733ecb?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> Report is 1 commits behind head on develop.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/json/pull/1057/graphs/tree.svg?width=650&height=150&src=pr&token=HNiMmIjyKi&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/json/pull/1057?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@           Coverage Diff            @@  
##           develop    #1057   +/-   ##  
========================================  
  Coverage    93.41%   93.41%             
========================================  
  Files           91       91             
  Lines         8667     8667             
========================================  
  Hits          8096     8096             
  Misses         571      571             
```  
  
| [Files with missing lines](https://app.codecov.io/gh/boostorg/json/pull/1057?dropdown=coverage&src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [...detail/charconv/detail/fast\_float/float\_common.hpp](https://app.codecov.io/gh/boostorg/json/pull/1057?src=pr&el=tree&filepath=include%2Fboost%2Fjson%2Fdetail%2Fcharconv%2Fdetail%2Ffast_float%2Ffloat_common.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9qc29uL2RldGFpbC9jaGFyY29udi9kZXRhaWwvZmFzdF9mbG9hdC9mbG9hdF9jb21tb24uaHBw) | `62.19% <ø> (ø)` | |  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/json/pull/1057?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/json/pull/1057?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [5bdbb23...b6c916f](https://app.codecov.io/gh/boostorg/json/pull/1057?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---

## Comment 3

> Username: cppalliance-bot  
> Created_at: 2024-10-18 08:45:23 UTC  
> Url: https://github.com/boostorg/json/pull/1057#issuecomment-2421856187  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/1057/pullrequest-condensed-79a3ed0.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/1057/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/1057/pullrequest.html)

---

## Comment 4

> Username: grisumbras  
> Created_at: 2024-10-22 13:20:41 UTC  
> Url: https://github.com/boostorg/json/pull/1057#issuecomment-2429271563  

Thank you for your contribution.

---
