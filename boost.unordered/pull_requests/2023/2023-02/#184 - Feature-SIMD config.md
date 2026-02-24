# #184 Feature/SIMD config [Merged]

> Username: joaquintides  
> Created at: 2023-02-24 17:26:14 UTC  
> Updated at: 2023-02-26 15:39:58 UTC  
> Merged at: 2023-02-26 15:39:50 UTC  
> Closed at: 2023-02-26 15:39:50 UTC  
> Url: https://github.com/boostorg/unordered/pull/184  

Though the changes are trivial, this feature opens up infinite possibilities for bikeshedding. Your opinion requested on the following:  
  
* Names ok? `BOOST_UNORDERED_..._LITTLE_ENDIAN_NEON` could be `BOOST_UNORDERED_..._NEON`, but this allows us more granularity in case we eventually support big-endian Neon.  
* Disabling, say Neon, does not guarantee that the non-SIMD variant will be used: SSE2 can still be automatically detected. Is there value in forcing non-SIMD?  
* Do we _document_ this? I'd rather not do it for the moment being. FWIW, `BOOST_URL_USE_SSE2` is not documented either.

---

## Comment 1

> Username: pdimov  
> Created_at: 2023-02-24 17:31:49 UTC  
> Url: https://github.com/boostorg/unordered/pull/184#issuecomment-1444093927  

I don't see why one would want to specifically disable little-endian NEON. BOOST_UNORDERED_DISABLE_NEON should be enough.

---

## Comment 2

> Username: joaquintides  
> Created_at: 2023-02-24 17:35:40 UTC  
> Url: https://github.com/boostorg/unordered/pull/184#issuecomment-1444101147  

a) `BOOST_UNORDERED_DISABLE_NEON`/`BOOST_UNORDERED_ENABLE_LITTLE_ENDIAN_NEON` or b) `BOOST_UNORDERED_DISABLE_NEON`/`BOOST_UNORDERED_ENABLE_NEON`? Note that blindly enabling Neon on a big-endian Neon platform will lead to either UB or a compile-time error (not sure which, really).

---

## Comment 3

> Username: pdimov  
> Created_at: 2023-02-24 19:01:28 UTC  
> Url: https://github.com/boostorg/unordered/pull/184#issuecomment-1444271334  

BOOST_UNORDERED_DISABLE_NEON/BOOST_UNORDERED_ENABLE_NEON, I think. The user will be responsible for whatever happens, these are "manual override" buttons that should be needed very rarely.

---

## Review 4 [Commented]

> Username: pdimov  
> Created_at: 2023-02-24 19:03:21 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/unordered/pull/184#pullrequestreview-1314111361  

📁 include/boost/unordered/detail/foa.hpp

```diff
  45 |     defined(_M_X64)||(defined(_M_IX86_FP)&&_M_IX86_FP>=2)
  44 |- #define BOOST_UNORDERED_SSE2
  46 |+ #define BOOST_UNORDERED_ENABLE_SSE2
```

> Username: pdimov  
> Created_at: 2023-02-24 19:03:20 UTC  
> Updated_at: 2023-02-24 19:03:21 UTC  
> Url: https://github.com/boostorg/unordered/pull/184#discussion_r1117501112  

I don't like mixing up macros defined by the user with macros defined by the library. ENABLE/DISABLE should only be set by the user and never by the library. We should retain our own macro and define it accordingly.


---

## Comment 5

> Username: pdimov  
> Created_at: 2023-02-24 20:16:01 UTC  
> Url: https://github.com/boostorg/unordered/pull/184#issuecomment-1444402542  

Perfect, thanks.  
  
Are we sure we want to undefine our SSE2/Neon macros at the end? It might be useful for a user to know whether SSE2/Neon is used or not.

---

## Comment 6

> Username: joaquintides  
> Created_at: 2023-02-24 21:13:24 UTC  
> Url: https://github.com/boostorg/unordered/pull/184#issuecomment-1444501855  

I think it's best not to leak these macros because they can be Hyrum-abused: we may, for instance, complete forego SSE2 in the future in favor of a revolutionary SWAR technique, and we'd be force to keep computing `BOOST_UNORDERED_SSE2` to not break users who take advantage of it for their own SSE2-detection.

---

## Comment 7

> Username: pdimov  
> Created_at: 2023-02-24 22:45:39 UTC  
> Url: https://github.com/boostorg/unordered/pull/184#issuecomment-1444610759  

That's their problem. The intended use of this macro is to detect whether we use SSE2, not whether someone else should.

---

## Comment 8

> Username: cmazakas  
> Created_at: 2023-02-24 23:45:00 UTC  
> Url: https://github.com/boostorg/unordered/pull/184#issuecomment-1444729791  

> That's their problem. The intended use of this macro is to detect whether we use SSE2, not whether someone else should.  
  
In general, I agree with this sentiment.  
  
Users would probably be very interested in making sure _our_ code is using SIMD. If they do choose to use us for detection logic, that was always kind of dicey, especially if we agree to not publicly document the macros.

---

## Comment 9

> Username: codecov[bot]  
> Created_at: 2023-02-25 06:14:02 UTC  
> Updated_at: 2023-02-25 19:23:50 UTC  
> Url: https://github.com/boostorg/unordered/pull/184#issuecomment-1445008012  

# [Codecov](https://codecov.io/gh/boostorg/unordered/pull/184?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#184](https://codecov.io/gh/boostorg/unordered/pull/184?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (533a2bb) into [develop](https://codecov.io/gh/boostorg/unordered/commit/719eb90d0360520b837bf931d46dc23490d305f6?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (719eb90) will **not change** coverage.  
> The diff coverage is `n/a`.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/unordered/pull/184/graphs/tree.svg?width=650&height=150&src=pr&token=ZqRPZlJZ5N&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://codecov.io/gh/boostorg/unordered/pull/184?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@           Coverage Diff            @@  
##           develop     #184   +/-   ##  
========================================  
  Coverage    97.90%   97.90%             
========================================  
  Files           89       89             
  Lines        13777    13777             
========================================  
  Hits         13488    13488             
  Misses         289      289             
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/unordered/pull/184?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/unordered/detail/foa.hpp](https://codecov.io/gh/boostorg/unordered/pull/184?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91bm9yZGVyZWQvZGV0YWlsL2ZvYS5ocHA=) | `99.83% <ø> (ø)` | |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/unordered/pull/184?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/unordered/pull/184?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [719eb90...533a2bb](https://codecov.io/gh/boostorg/unordered/pull/184?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---
