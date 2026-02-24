# #2575 Avoid warnings about unused variable [Merged]

> Username: mojca  
> Created at: 2022-12-04 09:15:11 UTC  
> Updated at: 2022-12-07 06:05:30 UTC  
> Merged at: 2022-12-07 05:41:43 UTC  
> Closed at: 2022-12-07 05:41:43 UTC  
> Url: https://github.com/boostorg/beast/pull/2575  

A fix for #2574.  
  
```  
C:\PathTo\boost\libs\beast\include\boost/beast/websocket/detail/impl_base.hpp(455,38): error C2220: the following warning is treated as an error  
C:\PathTo\boost\libs\beast\include\boost/beast/websocket/detail/impl_base.hpp(455,38): warning C4100: 'n_bytes': unreferenced formal parameter  
```

---

## Comment 1

> Username: cppalliance-bot  
> Created_at: 2022-12-04 09:44:14 UTC  
> Url: https://github.com/boostorg/beast/pull/2575#issuecomment-1336361958  

![pullrequest](https://2575.beast.tracing.cppalliance.org/pullrequest-9dd50fc9.png)  
Timeline tracing charts: [https://2575.beast.tracing.cppalliance.org/index.html](https://2575.beast.tracing.cppalliance.org/index.html)

---

## Comment 2

> Username: codecov[bot]  
> Created_at: 2022-12-04 10:07:35 UTC  
> Updated_at: 2022-12-04 10:17:29 UTC  
> Url: https://github.com/boostorg/beast/pull/2575#issuecomment-1336366161  

# [Codecov](https://codecov.io/gh/boostorg/beast/pull/2575?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#2575](https://codecov.io/gh/boostorg/beast/pull/2575?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (8ee9235) into [develop](https://codecov.io/gh/boostorg/beast/commit/b986b3b1b0dbd96e4426d29afa3da8f77c3b4da8?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (b986b3b) will **decrease** coverage by `0.00%`.  
> The diff coverage is `0.00%`.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/beast/pull/2575/graphs/tree.svg?width=650&height=150&src=pr&token=Gq6MFA9JRF&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://codecov.io/gh/boostorg/beast/pull/2575?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@             Coverage Diff             @@  
##           develop    #2575      +/-   ##  
===========================================  
- Coverage    94.73%   94.72%   -0.01%       
===========================================  
  Files          154      154                
  Lines        12027    12027                
===========================================  
- Hits         11394    11393       -1       
- Misses         633      634       +1       
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/beast/pull/2575?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/beast/websocket/detail/impl\_base.hpp](https://codecov.io/gh/boostorg/beast/pull/2575/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9iZWFzdC93ZWJzb2NrZXQvZGV0YWlsL2ltcGxfYmFzZS5ocHA=) | `85.71% <0.00%> (ø)` | |  
| [include/boost/beast/websocket/impl/ping.hpp](https://codecov.io/gh/boostorg/beast/pull/2575/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9iZWFzdC93ZWJzb2NrZXQvaW1wbC9waW5nLmhwcA==) | `94.06% <0.00%> (-0.85%)` | :arrow_down: |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/beast/pull/2575?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/beast/pull/2575?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [b986b3b...8ee9235](https://codecov.io/gh/boostorg/beast/pull/2575?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---
