# #1049 check for input size larger than allowed size of sequence [Merged]

> Username: grisumbras  
> Created at: 2024-10-07 15:09:39 UTC  
> Updated at: 2024-10-07 19:44:55 UTC  
> Merged at: 2024-10-07 19:41:18 UTC  
> Closed at: 2024-10-07 19:41:18 UTC  
> Url: https://github.com/boostorg/json/pull/1049  

Fix #1047  
Fix #1048

---

## Comment 1

> Username: cppalliance-bot  
> Created_at: 2024-10-07 15:17:26 UTC  
> Url: https://github.com/boostorg/json/pull/1049#issuecomment-2397222940  

An automated preview of the documentation is available at [https://1049.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html](https://1049.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html)

---

## Comment 2

> Username: codecov[bot]  
> Created_at: 2024-10-07 15:58:10 UTC  
> Updated_at: 2024-10-07 19:28:10 UTC  
> Url: https://github.com/boostorg/json/pull/1049#issuecomment-2397318201  

## [Codecov](https://app.codecov.io/gh/boostorg/json/pull/1049?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
All modified and coverable lines are covered by tests :white_check_mark:  
> Project coverage is 93.41%. Comparing base [(`85f9e92`)](https://app.codecov.io/gh/boostorg/json/commit/85f9e92c18283d42737ebd067df4f881824e4edb?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) to head [(`27c40ee`)](https://app.codecov.io/gh/boostorg/json/commit/27c40ee8c3038c5c329cd1cefcc0de52ec3d6970?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/json/pull/1049/graphs/tree.svg?width=650&height=150&src=pr&token=HNiMmIjyKi&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/json/pull/1049?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@             Coverage Diff             @@  
##           develop    #1049      +/-   ##  
===========================================  
- Coverage    93.41%   93.41%   -0.01%       
===========================================  
  Files           91       91                
  Lines         8672     8667       -5       
===========================================  
- Hits          8101     8096       -5       
  Misses         571      571                
```  
  
| [Flag](https://app.codecov.io/gh/boostorg/json/pull/1049/flags?src=pr&el=flags&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [](https://app.codecov.io/gh/boostorg/json/pull/1049/flags?src=pr&el=flag&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | `?` | |  
  
Flags with carried forward coverage won't be shown. [Click here](https://docs.codecov.io/docs/carryforward-flags?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#carryforward-flags-in-the-pull-request-comment) to find out more.  
  
| [Files with missing lines](https://app.codecov.io/gh/boostorg/json/pull/1049?dropdown=coverage&src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/json/detail/parse\_into.hpp](https://app.codecov.io/gh/boostorg/json/pull/1049?src=pr&el=tree&filepath=include%2Fboost%2Fjson%2Fdetail%2Fparse_into.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9qc29uL2RldGFpbC9wYXJzZV9pbnRvLmhwcA==) | `99.74% <100.00%> (-0.01%)` | :arrow_down: |  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/json/pull/1049?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/json/pull/1049?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [85f9e92...27c40ee](https://app.codecov.io/gh/boostorg/json/pull/1049?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---

## Comment 3

> Username: cppalliance-bot  
> Created_at: 2024-10-07 16:20:08 UTC  
> Url: https://github.com/boostorg/json/pull/1049#issuecomment-2397369438  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/1049/pullrequest-condensed-9750152.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/1049/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/1049/pullrequest.html)

---

## Comment 4

> Username: cppalliance-bot  
> Created_at: 2024-10-07 18:42:26 UTC  
> Url: https://github.com/boostorg/json/pull/1049#issuecomment-2397632938  

An automated preview of the documentation is available at [https://1049.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html](https://1049.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html)

---

## Comment 5

> Username: cppalliance-bot  
> Created_at: 2024-10-07 19:44:54 UTC  
> Url: https://github.com/boostorg/json/pull/1049#issuecomment-2397749134  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/1049/pullrequest-condensed-92fda81.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/1049/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/1049/pullrequest.html)

---
