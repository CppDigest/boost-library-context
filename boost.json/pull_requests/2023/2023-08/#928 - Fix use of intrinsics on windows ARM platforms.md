# #928 Fix use of intrinsics on windows ARM platforms [Merged]

> Username: mborland  
> Created at: 2023-08-11 13:22:34 UTC  
> Updated at: 2023-08-23 16:45:58 UTC  
> Merged at: 2023-08-23 16:45:58 UTC  
> Closed at: 2023-08-23 16:45:58 UTC  
> Url: https://github.com/boostorg/json/pull/928  

Closes: #926   
Closes: #927   
  
Changes use of intrinsics: https://learn.microsoft.com/en-us/cpp/intrinsics/umul128?view=msvc-170 and https://learn.microsoft.com/en-us/cpp/intrinsics/emul-emulu?view=msvc-170

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2023-08-11 13:42:26 UTC  
> Updated_at: 2023-08-16 12:32:25 UTC  
> Url: https://github.com/boostorg/json/pull/928#issuecomment-1674814774  

## [Codecov](https://app.codecov.io/gh/boostorg/json/pull/928?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#928](https://app.codecov.io/gh/boostorg/json/pull/928?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (0c1b469) into [develop](https://app.codecov.io/gh/boostorg/json/commit/e084a2918cc121528bcf58e04d026f5cbb89e262?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (e084a29) will **not change** coverage.  
> The diff coverage is `n/a`.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/json/pull/928/graphs/tree.svg?width=650&height=150&src=pr&token=HNiMmIjyKi&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/json/pull/928?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@           Coverage Diff            @@  
##           develop     #928   +/-   ##  
========================================  
  Coverage    92.95%   92.95%             
========================================  
  Files           85       85             
  Lines         8047     8047             
========================================  
  Hits          7480     7480             
  Misses         567      567             
```  
  
  
| [Files Changed](https://app.codecov.io/gh/boostorg/json/pull/928?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [.../boost/json/detail/charconv/detail/emulated128.hpp](https://app.codecov.io/gh/boostorg/json/pull/928?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9qc29uL2RldGFpbC9jaGFyY29udi9kZXRhaWwvZW11bGF0ZWQxMjguaHBw) | `0.00% <ø> (ø)` | |  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/json/pull/928?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/json/pull/928?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [e084a29...0c1b469](https://app.codecov.io/gh/boostorg/json/pull/928?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---

## Comment 2

> Username: cppalliance-bot  
> Created_at: 2023-08-11 14:35:48 UTC  
> Url: https://github.com/boostorg/json/pull/928#issuecomment-1674896332  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/928/pullrequest-condensed-cf6266b.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/928/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/928/pullrequest.html)

---

## Comment 3

> Username: cppalliance-bot  
> Created_at: 2023-08-16 13:20:45 UTC  
> Url: https://github.com/boostorg/json/pull/928#issuecomment-1680594918  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/928/pullrequest-condensed-4963266.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/928/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/928/pullrequest.html)

---

## Comment 4

> Username: grisumbras  
> Created_at: 2023-08-23 10:02:11 UTC  
> Url: https://github.com/boostorg/json/pull/928#issuecomment-1689668142  

I feel like rather than doing `if defined(BOOST_JSON_HAS_MSVC_64BIT_INTRINSICS) && !defined(_M_ARM64)` and similar, we should have `BOOST_JSON_HAS_MSVC_X86_64_INTRINSICS`, `BOOST_JSON_HAS_MSVC_ARM64_INTRINSICS` `BOOST_JSON_HAS_MSVC_X86_INTRINSICS`, `BOOST_JSON_HAS_MSVC_ARM32_INTRINSICS`. Or does the overlap make it impractical?

---

## Comment 5

> Username: mborland  
> Created_at: 2023-08-23 13:10:35 UTC  
> Url: https://github.com/boostorg/json/pull/928#issuecomment-1689938126  

> I feel like rather than doing `if defined(BOOST_JSON_HAS_MSVC_64BIT_INTRINSICS) && !defined(_M_ARM64)` and similar, we should have `BOOST_JSON_HAS_MSVC_X86_64_INTRINSICS`, `BOOST_JSON_HAS_MSVC_ARM64_INTRINSICS` `BOOST_JSON_HAS_MSVC_X86_INTRINSICS`, `BOOST_JSON_HAS_MSVC_ARM32_INTRINSICS`. Or does the overlap make it impractical?  
  
There is overlap in the 64-bit platforms with `__umulh`, and the bit scan intrinsics which are the primary ones used. If you want me to change it I can.

---

## Comment 6

> Username: grisumbras  
> Created_at: 2023-08-23 16:43:28 UTC  
> Url: https://github.com/boostorg/json/pull/928#issuecomment-1690293037  

Nah, let's merge it as-is. I'm expecting to remove it with dependency on Boost.Charconv soon anyway.

---
