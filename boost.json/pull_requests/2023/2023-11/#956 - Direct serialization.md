# #956 Direct serialization [Merged]

> Username: grisumbras  
> Created at: 2023-11-27 15:00:43 UTC  
> Updated at: 2024-09-16 19:19:39 UTC  
> Merged at: 2024-09-16 17:44:21 UTC  
> Closed at: 2024-09-16 17:44:21 UTC  
> Url: https://github.com/boostorg/json/pull/956  



---

## Comment 1

> Username: cppalliance-bot  
> Created_at: 2023-11-27 16:16:23 UTC  
> Url: https://github.com/boostorg/json/pull/956#issuecomment-1828157815  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/956/pullrequest-condensed-b572039.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/956/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/956/pullrequest.html)

---

## Comment 2

> Username: codecov[bot]  
> Created_at: 2023-12-01 15:35:45 UTC  
> Updated_at: 2024-09-16 19:19:39 UTC  
> Url: https://github.com/boostorg/json/pull/956#issuecomment-1836318596  

## [Codecov](https://app.codecov.io/gh/boostorg/json/pull/956?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
Attention: Patch coverage is `98.89381%` with `5 lines` in your changes missing coverage. Please review.  
> Project coverage is 93.41%. Comparing base [(`72b0e8a`)](https://app.codecov.io/gh/boostorg/json/commit/72b0e8a222648779fda808944b3923d3044124bc?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) to head [(`8e1fc20`)](https://app.codecov.io/gh/boostorg/json/commit/8e1fc20896fd9bfe2c0c8dacb26b8734bfe9768d?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> Report is 17 commits behind head on develop.  
  
| [Files with missing lines](https://app.codecov.io/gh/boostorg/json/pull/956?dropdown=coverage&src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Patch % | Lines |  
|---|---|---|  
| [include/boost/json/impl/serializer.hpp](https://app.codecov.io/gh/boostorg/json/pull/956?src=pr&el=tree&filepath=include%2Fboost%2Fjson%2Fimpl%2Fserializer.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9qc29uL2ltcGwvc2VyaWFsaXplci5ocHA=) | 97.70% | [4 Missing :warning: ](https://app.codecov.io/gh/boostorg/json/pull/956?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) |  
| [include/boost/json/impl/serializer.ipp](https://app.codecov.io/gh/boostorg/json/pull/956?src=pr&el=tree&filepath=include%2Fboost%2Fjson%2Fimpl%2Fserializer.ipp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9qc29uL2ltcGwvc2VyaWFsaXplci5pcHA=) | 99.36% | [1 Missing :warning: ](https://app.codecov.io/gh/boostorg/json/pull/956?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) |  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/json/pull/956/graphs/tree.svg?width=650&height=150&src=pr&token=HNiMmIjyKi&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/json/pull/956?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #956      +/-   ##  
===========================================  
+ Coverage    93.40%   93.41%   +0.01%       
===========================================  
  Files           87       91       +4       
  Lines         8622     8672      +50       
===========================================  
+ Hits          8053     8101      +48       
- Misses         569      571       +2       
```  
  
| [Files with missing lines](https://app.codecov.io/gh/boostorg/json/pull/956?dropdown=coverage&src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/json/basic\_parser.hpp](https://app.codecov.io/gh/boostorg/json/pull/956?src=pr&el=tree&filepath=include%2Fboost%2Fjson%2Fbasic_parser.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9qc29uL2Jhc2ljX3BhcnNlci5ocHA=) | `100.00% <ø> (ø)` | |  
| [include/boost/json/basic\_parser\_impl.hpp](https://app.codecov.io/gh/boostorg/json/pull/956?src=pr&el=tree&filepath=include%2Fboost%2Fjson%2Fbasic_parser_impl.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9qc29uL2Jhc2ljX3BhcnNlcl9pbXBsLmhwcA==) | `98.50% <100.00%> (+0.14%)` | :arrow_up: |  
| [include/boost/json/detail/impl/stack.hpp](https://app.codecov.io/gh/boostorg/json/pull/956?src=pr&el=tree&filepath=include%2Fboost%2Fjson%2Fdetail%2Fimpl%2Fstack.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9qc29uL2RldGFpbC9pbXBsL3N0YWNrLmhwcA==) | `100.00% <100.00%> (ø)` | |  
| [include/boost/json/detail/impl/stack.ipp](https://app.codecov.io/gh/boostorg/json/pull/956?src=pr&el=tree&filepath=include%2Fboost%2Fjson%2Fdetail%2Fimpl%2Fstack.ipp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9qc29uL2RldGFpbC9pbXBsL3N0YWNrLmlwcA==) | `100.00% <100.00%> (ø)` | |  
| [include/boost/json/detail/literals.hpp](https://app.codecov.io/gh/boostorg/json/pull/956?src=pr&el=tree&filepath=include%2Fboost%2Fjson%2Fdetail%2Fliterals.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9qc29uL2RldGFpbC9saXRlcmFscy5ocHA=) | `100.00% <100.00%> (ø)` | |  
| [include/boost/json/detail/stack.hpp](https://app.codecov.io/gh/boostorg/json/pull/956?src=pr&el=tree&filepath=include%2Fboost%2Fjson%2Fdetail%2Fstack.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9qc29uL2RldGFpbC9zdGFjay5ocHA=) | `100.00% <100.00%> (ø)` | |  
| [include/boost/json/impl/serialize.hpp](https://app.codecov.io/gh/boostorg/json/pull/956?src=pr&el=tree&filepath=include%2Fboost%2Fjson%2Fimpl%2Fserialize.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9qc29uL2ltcGwvc2VyaWFsaXplLmhwcA==) | `100.00% <100.00%> (ø)` | |  
| [include/boost/json/impl/serialize.ipp](https://app.codecov.io/gh/boostorg/json/pull/956?src=pr&el=tree&filepath=include%2Fboost%2Fjson%2Fimpl%2Fserialize.ipp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9qc29uL2ltcGwvc2VyaWFsaXplLmlwcA==) | `95.60% <ø> (ø)` | |  
| [include/boost/json/serializer.hpp](https://app.codecov.io/gh/boostorg/json/pull/956?src=pr&el=tree&filepath=include%2Fboost%2Fjson%2Fserializer.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9qc29uL3NlcmlhbGl6ZXIuaHBw) | `100.00% <ø> (ø)` | |  
| [include/boost/json/impl/serializer.ipp](https://app.codecov.io/gh/boostorg/json/pull/956?src=pr&el=tree&filepath=include%2Fboost%2Fjson%2Fimpl%2Fserializer.ipp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9qc29uL2ltcGwvc2VyaWFsaXplci5pcHA=) | `99.60% <99.36%> (-0.17%)` | :arrow_down: |  
| ... and [1 more](https://app.codecov.io/gh/boostorg/json/pull/956?src=pr&el=tree-more&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | |  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/json/pull/956?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/json/pull/956?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [72b0e8a...8e1fc20](https://app.codecov.io/gh/boostorg/json/pull/956?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---

## Comment 3

> Username: cppalliance-bot  
> Created_at: 2023-12-01 16:21:20 UTC  
> Url: https://github.com/boostorg/json/pull/956#issuecomment-1836403127  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/956/pullrequest-condensed-0b86aed.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/956/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/956/pullrequest.html)

---

## Comment 4

> Username: cppalliance-bot  
> Created_at: 2023-12-11 15:21:30 UTC  
> Url: https://github.com/boostorg/json/pull/956#issuecomment-1850294253  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/956/pullrequest-condensed-64803be.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/956/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/956/pullrequest.html)

---

## Comment 5

> Username: cppalliance-bot  
> Created_at: 2023-12-11 22:11:07 UTC  
> Url: https://github.com/boostorg/json/pull/956#issuecomment-1850973316  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/956/pullrequest-condensed-65dbf60.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/956/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/956/pullrequest.html)

---

## Comment 6

> Username: cppalliance-bot  
> Created_at: 2023-12-12 14:46:08 UTC  
> Url: https://github.com/boostorg/json/pull/956#issuecomment-1852178425  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/956/pullrequest-condensed-c43bb37.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/956/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/956/pullrequest.html)

---

## Comment 7

> Username: cppalliance-bot  
> Created_at: 2023-12-12 16:51:13 UTC  
> Url: https://github.com/boostorg/json/pull/956#issuecomment-1852426173  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/956/pullrequest-condensed-07709ab.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/956/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/956/pullrequest.html)

---

## Comment 8

> Username: cppalliance-bot  
> Created_at: 2023-12-14 11:31:08 UTC  
> Url: https://github.com/boostorg/json/pull/956#issuecomment-1855678547  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/956/pullrequest-condensed-69cdab0.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/956/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/956/pullrequest.html)

---

## Comment 9

> Username: cppalliance-bot  
> Created_at: 2023-12-14 15:21:11 UTC  
> Url: https://github.com/boostorg/json/pull/956#issuecomment-1856046353  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/956/pullrequest-condensed-deec22e.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/956/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/956/pullrequest.html)

---

## Comment 10

> Username: cppalliance-bot  
> Created_at: 2023-12-14 17:36:04 UTC  
> Url: https://github.com/boostorg/json/pull/956#issuecomment-1856285686  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/956/pullrequest-condensed-9682832.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/956/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/956/pullrequest.html)

---

## Comment 11

> Username: cppalliance-bot  
> Created_at: 2023-12-18 16:21:08 UTC  
> Url: https://github.com/boostorg/json/pull/956#issuecomment-1860951571  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/956/pullrequest-condensed-ec0fd81.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/956/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/956/pullrequest.html)

---

## Comment 12

> Username: cppalliance-bot  
> Created_at: 2023-12-20 08:16:05 UTC  
> Url: https://github.com/boostorg/json/pull/956#issuecomment-1864043382  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/956/pullrequest-condensed-783904d.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/956/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/956/pullrequest.html)

---

## Comment 13

> Username: cppalliance-bot  
> Created_at: 2023-12-20 12:11:01 UTC  
> Url: https://github.com/boostorg/json/pull/956#issuecomment-1864370080  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/956/pullrequest-condensed-948bc84.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/956/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/956/pullrequest.html)

---

## Comment 14

> Username: cppalliance-bot  
> Created_at: 2023-12-21 09:55:54 UTC  
> Url: https://github.com/boostorg/json/pull/956#issuecomment-1865969719  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/956/pullrequest-condensed-4a5c2cb.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/956/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/956/pullrequest.html)

---

## Comment 15

> Username: cppalliance-bot  
> Created_at: 2023-12-21 13:45:53 UTC  
> Url: https://github.com/boostorg/json/pull/956#issuecomment-1866275980  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/956/pullrequest-condensed-faa038c.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/956/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/956/pullrequest.html)

---

## Comment 16

> Username: cppalliance-bot  
> Created_at: 2023-12-21 18:05:59 UTC  
> Url: https://github.com/boostorg/json/pull/956#issuecomment-1866726088  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/956/pullrequest-condensed-9290efb.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/956/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/956/pullrequest.html)

---

## Comment 17

> Username: cppalliance-bot  
> Created_at: 2023-12-21 19:40:54 UTC  
> Url: https://github.com/boostorg/json/pull/956#issuecomment-1866836041  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/956/pullrequest-condensed-8917c14.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/956/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/956/pullrequest.html)

---

## Comment 18

> Username: cppalliance-bot  
> Created_at: 2023-12-22 06:46:00 UTC  
> Url: https://github.com/boostorg/json/pull/956#issuecomment-1867305252  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/956/pullrequest-condensed-78a38da.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/956/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/956/pullrequest.html)

---

## Comment 19

> Username: cppalliance-bot  
> Created_at: 2023-12-22 09:26:03 UTC  
> Url: https://github.com/boostorg/json/pull/956#issuecomment-1867448497  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/956/pullrequest-condensed-fe463aa.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/956/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/956/pullrequest.html)

---

## Comment 20

> Username: cppalliance-bot  
> Created_at: 2023-12-22 11:45:58 UTC  
> Url: https://github.com/boostorg/json/pull/956#issuecomment-1867591636  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/956/pullrequest-condensed-6317e57.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/956/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/956/pullrequest.html)

---

## Comment 21

> Username: cppalliance-bot  
> Created_at: 2023-12-22 15:16:03 UTC  
> Url: https://github.com/boostorg/json/pull/956#issuecomment-1867802091  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/956/pullrequest-condensed-48badc5.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/956/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/956/pullrequest.html)

---

## Comment 22

> Username: cppalliance-bot  
> Created_at: 2023-12-28 10:32:06 UTC  
> Url: https://github.com/boostorg/json/pull/956#issuecomment-1871037332  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/956/pullrequest-condensed-a2f5dec.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/956/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/956/pullrequest.html)

---

## Comment 23

> Username: cppalliance-bot  
> Created_at: 2023-12-28 11:57:00 UTC  
> Url: https://github.com/boostorg/json/pull/956#issuecomment-1871098167  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/956/pullrequest-condensed-212cdeb.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/956/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/956/pullrequest.html)

---

## Comment 24

> Username: cppalliance-bot  
> Created_at: 2023-12-28 13:41:54 UTC  
> Url: https://github.com/boostorg/json/pull/956#issuecomment-1871189473  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/956/pullrequest-condensed-708e794.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/956/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/956/pullrequest.html)

---

## Comment 25

> Username: cppalliance-bot  
> Created_at: 2023-12-28 17:36:55 UTC  
> Url: https://github.com/boostorg/json/pull/956#issuecomment-1871369866  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/956/pullrequest-condensed-922fba7.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/956/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/956/pullrequest.html)

---

## Comment 26

> Username: cppalliance-bot  
> Created_at: 2023-12-29 13:42:00 UTC  
> Url: https://github.com/boostorg/json/pull/956#issuecomment-1872098646  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/956/pullrequest-condensed-cef7100.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/956/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/956/pullrequest.html)

---

## Comment 27

> Username: cppalliance-bot  
> Created_at: 2023-12-29 15:37:04 UTC  
> Url: https://github.com/boostorg/json/pull/956#issuecomment-1872174176  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/956/pullrequest-condensed-5bca2bc.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/956/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/956/pullrequest.html)

---

## Comment 28

> Username: cppalliance-bot  
> Created_at: 2024-01-12 14:17:05 UTC  
> Url: https://github.com/boostorg/json/pull/956#issuecomment-1889326405  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/956/pullrequest-condensed-c187943.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/956/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/956/pullrequest.html)

---

## Comment 29

> Username: cppalliance-bot  
> Created_at: 2024-01-22 08:55:44 UTC  
> Url: https://github.com/boostorg/json/pull/956#issuecomment-1903525143  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/956/pullrequest-condensed-f0cd87c.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/956/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/956/pullrequest.html)

---

## Comment 30

> Username: cppalliance-bot  
> Created_at: 2024-01-22 16:01:58 UTC  
> Url: https://github.com/boostorg/json/pull/956#issuecomment-1904314287  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/956/pullrequest-condensed-fe530c8.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/956/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/956/pullrequest.html)

---

## Comment 31

> Username: cppalliance-bot  
> Created_at: 2024-01-22 23:21:57 UTC  
> Url: https://github.com/boostorg/json/pull/956#issuecomment-1905007533  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/956/pullrequest-condensed-44d37dc.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/956/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/956/pullrequest.html)

---

## Comment 32

> Username: cppalliance-bot  
> Created_at: 2024-01-23 15:12:01 UTC  
> Url: https://github.com/boostorg/json/pull/956#issuecomment-1906257002  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/956/pullrequest-condensed-ecbd1ff.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/956/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/956/pullrequest.html)

---

## Comment 33

> Username: cppalliance-bot  
> Created_at: 2024-01-23 19:12:04 UTC  
> Url: https://github.com/boostorg/json/pull/956#issuecomment-1906752234  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/956/pullrequest-condensed-c85a525.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/956/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/956/pullrequest.html)

---

## Comment 34

> Username: cppalliance-bot  
> Created_at: 2024-01-25 18:42:07 UTC  
> Url: https://github.com/boostorg/json/pull/956#issuecomment-1910780852  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/956/pullrequest-condensed-a2546c2.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/956/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/956/pullrequest.html)

---

## Comment 35

> Username: cppalliance-bot  
> Created_at: 2024-01-26 15:52:15 UTC  
> Url: https://github.com/boostorg/json/pull/956#issuecomment-1912286799  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/956/pullrequest-condensed-46f3398.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/956/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/956/pullrequest.html)

---

## Comment 36

> Username: cppalliance-bot  
> Created_at: 2024-01-29 17:22:22 UTC  
> Url: https://github.com/boostorg/json/pull/956#issuecomment-1915214010  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/956/pullrequest-condensed-15c1866.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/956/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/956/pullrequest.html)

---

## Comment 37

> Username: cppalliance-bot  
> Created_at: 2024-06-26 08:47:23 UTC  
> Url: https://github.com/boostorg/json/pull/956#issuecomment-2191155344  

An automated preview of the documentation is available at [https://956.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html](https://956.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html)

---

## Comment 38

> Username: cppalliance-bot  
> Created_at: 2024-06-26 09:52:04 UTC  
> Url: https://github.com/boostorg/json/pull/956#issuecomment-2191282902  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/956/pullrequest-condensed-edc82a7.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/956/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/956/pullrequest.html)

---

## Comment 39

> Username: cppalliance-bot  
> Created_at: 2024-06-26 10:07:26 UTC  
> Url: https://github.com/boostorg/json/pull/956#issuecomment-2191312160  

An automated preview of the documentation is available at [https://956.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html](https://956.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html)

---

## Comment 40

> Username: cppalliance-bot  
> Created_at: 2024-06-26 11:16:55 UTC  
> Url: https://github.com/boostorg/json/pull/956#issuecomment-2191442503  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/956/pullrequest-condensed-52663be.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/956/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/956/pullrequest.html)

---

## Comment 41

> Username: cppalliance-bot  
> Created_at: 2024-06-26 13:27:30 UTC  
> Url: https://github.com/boostorg/json/pull/956#issuecomment-2191703045  

An automated preview of the documentation is available at [https://956.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html](https://956.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html)

---

## Comment 42

> Username: cppalliance-bot  
> Created_at: 2024-06-26 14:36:57 UTC  
> Url: https://github.com/boostorg/json/pull/956#issuecomment-2191875967  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/956/pullrequest-condensed-a560f1c.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/956/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/956/pullrequest.html)

---

## Comment 43

> Username: cppalliance-bot  
> Created_at: 2024-06-26 16:12:28 UTC  
> Url: https://github.com/boostorg/json/pull/956#issuecomment-2192089044  

An automated preview of the documentation is available at [https://956.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html](https://956.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html)

---

## Comment 44

> Username: cppalliance-bot  
> Created_at: 2024-06-26 17:22:04 UTC  
> Url: https://github.com/boostorg/json/pull/956#issuecomment-2192253405  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/956/pullrequest-condensed-9c266e5.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/956/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/956/pullrequest.html)

---

## Comment 45

> Username: cppalliance-bot  
> Created_at: 2024-07-02 07:12:25 UTC  
> Url: https://github.com/boostorg/json/pull/956#issuecomment-2202133970  

An automated preview of the documentation is available at [https://956.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html](https://956.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html)

---

## Comment 46

> Username: cppalliance-bot  
> Created_at: 2024-07-02 08:22:13 UTC  
> Url: https://github.com/boostorg/json/pull/956#issuecomment-2202290317  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/956/pullrequest-condensed-6ccfbd5.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/956/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/956/pullrequest.html)

---

## Comment 47

> Username: cppalliance-bot  
> Created_at: 2024-07-02 11:27:25 UTC  
> Url: https://github.com/boostorg/json/pull/956#issuecomment-2202863456  

An automated preview of the documentation is available at [https://956.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html](https://956.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html)

---

## Comment 48

> Username: cppalliance-bot  
> Created_at: 2024-07-02 11:52:25 UTC  
> Url: https://github.com/boostorg/json/pull/956#issuecomment-2202966470  

An automated preview of the documentation is available at [https://956.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html](https://956.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html)

---

## Comment 49

> Username: cppalliance-bot  
> Created_at: 2024-07-02 13:02:06 UTC  
> Url: https://github.com/boostorg/json/pull/956#issuecomment-2203102434  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/956/pullrequest-condensed-6d6ce67.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/956/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/956/pullrequest.html)

---

## Comment 50

> Username: cppalliance-bot  
> Created_at: 2024-07-02 19:17:28 UTC  
> Url: https://github.com/boostorg/json/pull/956#issuecomment-2204113260  

An automated preview of the documentation is available at [https://956.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html](https://956.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html)

---

## Comment 51

> Username: cppalliance-bot  
> Created_at: 2024-07-02 20:22:07 UTC  
> Url: https://github.com/boostorg/json/pull/956#issuecomment-2204327832  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/956/pullrequest-condensed-aab04e9.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/956/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/956/pullrequest.html)

---

## Comment 52

> Username: cppalliance-bot  
> Created_at: 2024-07-05 13:32:30 UTC  
> Url: https://github.com/boostorg/json/pull/956#issuecomment-2210887340  

An automated preview of the documentation is available at [https://956.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html](https://956.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html)

---

## Comment 53

> Username: cppalliance-bot  
> Created_at: 2024-07-05 14:42:07 UTC  
> Url: https://github.com/boostorg/json/pull/956#issuecomment-2211000609  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/956/pullrequest-condensed-d0276bf.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/956/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/956/pullrequest.html)

---

## Comment 54

> Username: cppalliance-bot  
> Created_at: 2024-07-05 20:03:39 UTC  
> Url: https://github.com/boostorg/json/pull/956#issuecomment-2211359138  

An automated preview of the documentation is available at [https://956.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html](https://956.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html)

---

## Comment 55

> Username: cppalliance-bot  
> Created_at: 2024-07-05 21:13:17 UTC  
> Url: https://github.com/boostorg/json/pull/956#issuecomment-2211418268  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/956/pullrequest-condensed-34ab836.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/956/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/956/pullrequest.html)

---

## Comment 56

> Username: cppalliance-bot  
> Created_at: 2024-07-07 17:47:22 UTC  
> Url: https://github.com/boostorg/json/pull/956#issuecomment-2212521078  

An automated preview of the documentation is available at [https://956.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html](https://956.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html)

---

## Comment 57

> Username: cppalliance-bot  
> Created_at: 2024-07-07 18:57:03 UTC  
> Url: https://github.com/boostorg/json/pull/956#issuecomment-2212538636  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/956/pullrequest-condensed-2b01a04.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/956/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/956/pullrequest.html)

---

## Comment 58

> Username: cppalliance-bot  
> Created_at: 2024-07-08 11:12:26 UTC  
> Url: https://github.com/boostorg/json/pull/956#issuecomment-2213714818  

An automated preview of the documentation is available at [https://956.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html](https://956.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html)

---

## Comment 59

> Username: cppalliance-bot  
> Created_at: 2024-07-08 13:40:40 UTC  
> Url: https://github.com/boostorg/json/pull/956#issuecomment-2214109890  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/956/pullrequest-condensed-b48c514.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/956/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/956/pullrequest.html)

---

## Comment 60

> Username: cppalliance-bot  
> Created_at: 2024-08-12 04:07:31 UTC  
> Url: https://github.com/boostorg/json/pull/956#issuecomment-2283078839  

An automated preview of the documentation is available at [https://956.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html](https://956.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html)

---

## Comment 61

> Username: cppalliance-bot  
> Created_at: 2024-08-12 05:10:19 UTC  
> Url: https://github.com/boostorg/json/pull/956#issuecomment-2283123015  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/956/pullrequest-condensed-a828b7a.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/956/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/956/pullrequest.html)

---

## Comment 62

> Username: cppalliance-bot  
> Created_at: 2024-08-18 08:02:25 UTC  
> Url: https://github.com/boostorg/json/pull/956#issuecomment-2295167928  

An automated preview of the documentation is available at [https://956.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html](https://956.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html)

---

## Comment 63

> Username: cppalliance-bot  
> Created_at: 2024-08-18 09:05:10 UTC  
> Url: https://github.com/boostorg/json/pull/956#issuecomment-2295186449  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/956/pullrequest-condensed-d930c25.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/956/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/956/pullrequest.html)

---

## Comment 64

> Username: cppalliance-bot  
> Created_at: 2024-09-10 13:27:28 UTC  
> Url: https://github.com/boostorg/json/pull/956#issuecomment-2340784503  

An automated preview of the documentation is available at [https://956.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html](https://956.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html)

---

## Comment 65

> Username: cppalliance-bot  
> Created_at: 2024-09-10 14:30:00 UTC  
> Url: https://github.com/boostorg/json/pull/956#issuecomment-2341004844  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/956/pullrequest-condensed-d5dfa1c.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/956/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/956/pullrequest.html)

---

## Comment 66

> Username: cppalliance-bot  
> Created_at: 2024-09-16 16:25:23 UTC  
> Url: https://github.com/boostorg/json/pull/956#issuecomment-2353379271  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/956/pullrequest-condensed-523c848.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/956/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/956/pullrequest.html)

---

## Comment 67

> Username: cppalliance-bot  
> Created_at: 2024-09-16 16:47:30 UTC  
> Url: https://github.com/boostorg/json/pull/956#issuecomment-2353425194  

An automated preview of the documentation is available at [https://956.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html](https://956.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html)

---

## Comment 68

> Username: cppalliance-bot  
> Created_at: 2024-09-16 17:49:58 UTC  
> Url: https://github.com/boostorg/json/pull/956#issuecomment-2353545664  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/956/pullrequest-condensed-62bfe07.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/956/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/956/pullrequest.html)

---
