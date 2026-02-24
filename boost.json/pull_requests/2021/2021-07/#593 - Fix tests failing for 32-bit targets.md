# #593 Fix tests failing for 32-bit targets [Open]

> Username: grisumbras  
> Created at: 2021-07-06 19:00:27 UTC  
> Updated at: 2021-07-12 18:03:28 UTC  
> Url: https://github.com/boostorg/json/pull/593  

When an integer is converted to a floating point type which cannot hold it without loss of precision, the conversion is implementation-defined. This apparently means, that the conversion can result in slightly different results for the same number in different functions. Because of this several tests were failing for some 32 bit targets with optimizations enabled (#576). This change removes those tests and instead adds an equivalent test that uses an integer value that can be converted to float without loss of precision.  
  
The question is, should we remove other similar tests that involve lossy conversions? Also, maybe instead of removing those tests, we should check that the results are within and allowed interval?

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2021-07-06 19:25:44 UTC  
> Url: https://github.com/boostorg/json/pull/593#issuecomment-875023246  

# [Codecov](https://codecov.io/gh/boostorg/json/pull/593?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#593](https://codecov.io/gh/boostorg/json/pull/593?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (ccf41db) into [develop](https://codecov.io/gh/boostorg/json/commit/745be889163fd115613c9f2e45336565ded36bc5?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (745be88) will **not change** coverage.  
> The diff coverage is `n/a`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/json/pull/593/graphs/tree.svg?width=650&height=150&src=pr&token=HNiMmIjyKi&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://codecov.io/gh/boostorg/json/pull/593?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@           Coverage Diff            @@  
##           develop     #593   +/-   ##  
========================================  
  Coverage    99.05%   99.05%             
========================================  
  Files           68       68             
  Lines         6114     6114             
========================================  
  Hits          6056     6056             
  Misses          58       58             
```  
  
  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/json/pull/593?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/json/pull/593?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [745be88...ccf41db](https://codecov.io/gh/boostorg/json/pull/593?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).

---

## Comment 2

> Username: cppalliance-bot  
> Created_at: 2021-07-06 20:14:07 UTC  
> Url: https://github.com/boostorg/json/pull/593#issuecomment-875051241  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/593/pullrequest-condensed-b66241f.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/593/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/593/pullrequest.html)

---

## Comment 3

> Username: vinniefalco  
> Created_at: 2021-07-12 17:55:11 UTC  
> Url: https://github.com/boostorg/json/pull/593#issuecomment-878476456  

Did this get resolved? Or merged?

---

## Comment 4

> Username: grisumbras  
> Created_at: 2021-07-12 18:03:28 UTC  
> Url: https://github.com/boostorg/json/pull/593#issuecomment-878481961  

No, I'm working on an alternative that doesn't simply drop `EQUF` tests, but replaces them with different tests.

---
