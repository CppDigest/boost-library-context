# #884 Feature: serialize special numbers [Merged]

> Username: grisumbras  
> Created at: 2023-05-07 18:26:50 UTC  
> Updated at: 2023-06-01 10:52:04 UTC  
> Merged at: 2023-05-27 09:19:36 UTC  
> Closed at: 2023-05-27 09:19:37 UTC  
> Url: https://github.com/boostorg/json/pull/884  

1. Change serializer to output special floating point numbers differently, so that the output is always valid JSON.  
    * Infinity is output as `1e99999` (this number is so big that it is parsed by every implementation I am aware of as FP infinity).  
    * NaN is output as `null`. It changes the type, and roundtrip won't happen, but at least it's not something that is not JSON.  
2. Add serializer option to enable an extension (the extension is incidentally the current behaviour)  
    * Infinity is output as `Infinity`.  
    * NaN is output as `NaN`.  
3. Add the ability to control serialisation options when outputting into ostreams.  
  
The benefit for the second behaviour is that infinity and NaN are using unambiguous special representation.  
  
Fix #350

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2023-05-07 18:43:28 UTC  
> Updated_at: 2023-05-27 09:18:50 UTC  
> Url: https://github.com/boostorg/json/pull/884#issuecomment-1537514619  

## [Codecov](https://app.codecov.io/gh/boostorg/json/pull/884?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#884](https://app.codecov.io/gh/boostorg/json/pull/884?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (a4ecd74) into [develop](https://app.codecov.io/gh/boostorg/json/commit/ac9080ca0e211469f99b0ee1505a77826a5f2413?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (ac9080c) will **increase** coverage by `0.00%`.  
> The diff coverage is `100.00%`.  
  
> :exclamation: Current head a4ecd74 differs from pull request most recent head 6a12c32. Consider uploading reports for the commit 6a12c32 to get more accurate results  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/json/pull/884/graphs/tree.svg?width=650&height=150&src=pr&token=HNiMmIjyKi&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/json/pull/884?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@           Coverage Diff            @@  
##           develop     #884   +/-   ##  
========================================  
  Coverage    99.24%   99.24%             
========================================  
  Files           71       71             
  Lines         6861     6889   +28       
========================================  
+ Hits          6809     6837   +28       
  Misses          52       52             
```  
  
  
| [Impacted Files](https://app.codecov.io/gh/boostorg/json/pull/884?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/json/serializer.hpp](https://app.codecov.io/gh/boostorg/json/pull/884?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9qc29uL3NlcmlhbGl6ZXIuaHBw) | `100.00% <ø> (ø)` | |  
| [include/boost/json/detail/impl/format.ipp](https://app.codecov.io/gh/boostorg/json/pull/884?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9qc29uL2RldGFpbC9pbXBsL2Zvcm1hdC5pcHA=) | `100.00% <100.00%> (ø)` | |  
| [include/boost/json/detail/ryu/detail/common.hpp](https://app.codecov.io/gh/boostorg/json/pull/884?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9qc29uL2RldGFpbC9yeXUvZGV0YWlsL2NvbW1vbi5ocHA=) | `100.00% <100.00%> (ø)` | |  
| [include/boost/json/detail/ryu/impl/d2s.ipp](https://app.codecov.io/gh/boostorg/json/pull/884?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9qc29uL2RldGFpbC9yeXUvaW1wbC9kMnMuaXBw) | `99.55% <100.00%> (+<0.01%)` | :arrow_up: |  
| [include/boost/json/impl/serialize.ipp](https://app.codecov.io/gh/boostorg/json/pull/884?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9qc29uL2ltcGwvc2VyaWFsaXplLmlwcA==) | `95.60% <100.00%> (+0.66%)` | :arrow_up: |  
| [include/boost/json/impl/serializer.ipp](https://app.codecov.io/gh/boostorg/json/pull/884?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9qc29uL2ltcGwvc2VyaWFsaXplci5pcHA=) | `98.63% <100.00%> (+<0.01%)` | :arrow_up: |  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/json/pull/884?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/json/pull/884?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [ac9080c...6a12c32](https://app.codecov.io/gh/boostorg/json/pull/884?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---

## Comment 2

> Username: cppalliance-bot  
> Created_at: 2023-05-07 19:40:44 UTC  
> Url: https://github.com/boostorg/json/pull/884#issuecomment-1537524975  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/884/pullrequest-condensed-d495e69.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/884/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/884/pullrequest.html)

---

## Comment 3

> Username: cppalliance-bot  
> Created_at: 2023-05-08 10:18:02 UTC  
> Url: https://github.com/boostorg/json/pull/884#issuecomment-1538127962  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/884/pullrequest-condensed-d0c545e.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/884/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/884/pullrequest.html)

---

## Comment 4

> Username: cppalliance-bot  
> Created_at: 2023-05-27 09:55:36 UTC  
> Url: https://github.com/boostorg/json/pull/884#issuecomment-1565311525  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/884/pullrequest-condensed-5a36d5f.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/884/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/884/pullrequest.html)

---
