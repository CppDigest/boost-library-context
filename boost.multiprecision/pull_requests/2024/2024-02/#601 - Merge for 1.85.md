# #601 Merge for 1.85 [Merged]

> Username: mborland  
> Created at: 2024-02-28 07:17:55 UTC  
> Updated at: 2024-03-03 09:18:22 UTC  
> Merged at: 2024-03-03 09:18:22 UTC  
> Closed at: 2024-03-03 09:18:22 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/601  

@jzmaddock, @ckormanyos, and @NAThompson same question here. Any last minute things that need to get added?

---

## Comment 1

> Username: jzmaddock  
> Created_at: 2024-02-28 09:42:34 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/601#issuecomment-1968593914  

I suspect the new testing failures are issues in the updated Math coverage tests noy being multiprecision safe.  Running locally now to investigate...

---

## Comment 2

> Username: codecov[bot]  
> Created_at: 2024-02-28 09:43:39 UTC  
> Updated_at: 2024-02-29 08:42:48 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/601#issuecomment-1968598459  

## [Codecov](https://app.codecov.io/gh/boostorg/multiprecision/pull/601?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
Attention: Patch coverage is `99.37888%` with `1 lines` in your changes are missing coverage. Please review.  
> :exclamation: No coverage uploaded for pull request base (`master@246dbe5`). [Click here to learn what that means](https://docs.codecov.io/docs/error-reference?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#section-missing-base-commit).  
  
> :exclamation: Current head 2766661 differs from pull request most recent head f0319ec. Consider uploading reports for the commit f0319ec to get more accurate results  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/multiprecision/pull/601/graphs/tree.svg?width=650&height=150&src=pr&token=SDDBym7Pc9&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/multiprecision/pull/601?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@           Coverage Diff            @@  
##             master    #601   +/-   ##  
========================================  
  Coverage          ?   94.1%             
========================================  
  Files             ?     273             
  Lines             ?   28525             
  Branches          ?       0             
========================================  
  Hits              ?   26834             
  Misses            ?    1691             
  Partials          ?       0             
```  
  
  
| [Files](https://app.codecov.io/gh/boostorg/multiprecision/pull/601?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/multiprecision/complex\_adaptor.hpp](https://app.codecov.io/gh/boostorg/multiprecision/pull/601?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9tdWx0aXByZWNpc2lvbi9jb21wbGV4X2FkYXB0b3IuaHBw) | `89.5% <ø> (ø)` | |  
| [include/boost/multiprecision/cpp\_bin\_float.hpp](https://app.codecov.io/gh/boostorg/multiprecision/pull/601?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9tdWx0aXByZWNpc2lvbi9jcHBfYmluX2Zsb2F0LmhwcA==) | `89.2% <100.0%> (ø)` | |  
| [include/boost/multiprecision/cpp\_dec\_float.hpp](https://app.codecov.io/gh/boostorg/multiprecision/pull/601?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9tdWx0aXByZWNpc2lvbi9jcHBfZGVjX2Zsb2F0LmhwcA==) | `94.0% <100.0%> (ø)` | |  
| [include/boost/multiprecision/cpp\_int/limits.hpp](https://app.codecov.io/gh/boostorg/multiprecision/pull/601?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9tdWx0aXByZWNpc2lvbi9jcHBfaW50L2xpbWl0cy5ocHA=) | `100.0% <ø> (ø)` | |  
| [...ude/boost/multiprecision/detail/string\_helpers.hpp](https://app.codecov.io/gh/boostorg/multiprecision/pull/601?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9tdWx0aXByZWNpc2lvbi9kZXRhaWwvc3RyaW5nX2hlbHBlcnMuaHBw) | `100.0% <100.0%> (ø)` | |  
| [include/boost/multiprecision/gmp.hpp](https://app.codecov.io/gh/boostorg/multiprecision/pull/601?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9tdWx0aXByZWNpc2lvbi9nbXAuaHBw) | `93.5% <ø> (ø)` | |  
| [test/git\_issue\_573.cpp](https://app.codecov.io/gh/boostorg/multiprecision/pull/601?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC9naXRfaXNzdWVfNTczLmNwcA==) | `100.0% <100.0%> (ø)` | |  
| [test/git\_issue\_576.cpp](https://app.codecov.io/gh/boostorg/multiprecision/pull/601?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC9naXRfaXNzdWVfNTc2LmNwcA==) | `100.0% <100.0%> (ø)` | |  
| [test/test\_arithmetic.hpp](https://app.codecov.io/gh/boostorg/multiprecision/pull/601?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC90ZXN0X2FyaXRobWV0aWMuaHBw) | `99.4% <100.0%> (ø)` | |  
| [test/test\_arithmetic\_cpp\_bin\_float\_6.cpp](https://app.codecov.io/gh/boostorg/multiprecision/pull/601?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC90ZXN0X2FyaXRobWV0aWNfY3BwX2Jpbl9mbG9hdF82LmNwcA==) | `100.0% <100.0%> (ø)` | |  
| ... and [1 more](https://app.codecov.io/gh/boostorg/multiprecision/pull/601?src=pr&el=tree-more&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | |  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/multiprecision/pull/601?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/multiprecision/pull/601?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [246dbe5...f0319ec](https://app.codecov.io/gh/boostorg/multiprecision/pull/601?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---

## Comment 3

> Username: mborland  
> Created_at: 2024-02-28 11:09:51 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/601#issuecomment-1968753512  

> I suspect the new testing failures are issues in the updated Math coverage tests noy being multiprecision safe. Running locally now to investigate...  
  
I will hold off on merging https://github.com/boostorg/math/pull/1104 then.

---

## Comment 4

> Username: jzmaddock  
> Created_at: 2024-03-03 09:18:17 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/601#issuecomment-1975097306  

Drone failure is a network timeout, merging...

---
