# #1083 Units compatibility for optimization [Merged]

> Username: NAThompson  
> Created at: 2024-02-10 20:36:35 UTC  
> Updated at: 2024-02-11 04:58:22 UTC  
> Merged at: 2024-02-11 04:58:18 UTC  
> Closed at: 2024-02-11 04:58:18 UTC  
> Url: https://github.com/boostorg/math/pull/1083  



---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2024-02-11 01:51:23 UTC  
> Updated_at: 2024-02-11 04:42:07 UTC  
> Url: https://github.com/boostorg/math/pull/1083#issuecomment-1937390431  

## [Codecov](https://app.codecov.io/gh/boostorg/math/pull/1083?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
All modified and coverable lines are covered by tests :white_check_mark:  
> Comparison is base [(`91f3a22`)](https://app.codecov.io/gh/boostorg/math/commit/91f3a221699c55ffbe00d5241154e5e940e39090?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) 88.59% compared to head [(`5ed273d`)](https://app.codecov.io/gh/boostorg/math/pull/1083?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) 88.61%.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/math/pull/1083/graphs/tree.svg?width=650&height=150&src=pr&token=26IDb1OAa7&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/math/pull/1083?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@             Coverage Diff             @@  
##           develop    #1083      +/-   ##  
===========================================  
+ Coverage    88.59%   88.61%   +0.02%       
===========================================  
  Files          767      767                
  Lines        64518    64518                
===========================================  
+ Hits         57158    57174      +16       
+ Misses        7360     7344      -16       
```  
  
  
| [Files](https://app.codecov.io/gh/boostorg/math/pull/1083?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/math/optimization/detail/common.hpp](https://app.codecov.io/gh/boostorg/math/pull/1083?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9tYXRoL29wdGltaXphdGlvbi9kZXRhaWwvY29tbW9uLmhwcA==) | `40.86% <100.00%> (ø)` | |  
| [...boost/math/optimization/differential\_evolution.hpp](https://app.codecov.io/gh/boostorg/math/pull/1083?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9tYXRoL29wdGltaXphdGlvbi9kaWZmZXJlbnRpYWxfZXZvbHV0aW9uLmhwcA==) | `82.00% <100.00%> (ø)` | |  
| [include/boost/math/optimization/jso.hpp](https://app.codecov.io/gh/boostorg/math/pull/1083?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9tYXRoL29wdGltaXphdGlvbi9qc28uaHBw) | `81.21% <100.00%> (ø)` | |  
| [include/boost/math/optimization/random\_search.hpp](https://app.codecov.io/gh/boostorg/math/pull/1083?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9tYXRoL29wdGltaXphdGlvbi9yYW5kb21fc2VhcmNoLmhwcA==) | `72.72% <100.00%> (ø)` | |  
| [test/differential\_evolution\_test.cpp](https://app.codecov.io/gh/boostorg/math/pull/1083?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC9kaWZmZXJlbnRpYWxfZXZvbHV0aW9uX3Rlc3QuY3Bw) | `59.70% <ø> (ø)` | |  
| [test/jso\_test.cpp](https://app.codecov.io/gh/boostorg/math/pull/1083?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC9qc29fdGVzdC5jcHA=) | `100.00% <ø> (ø)` | |  
| [test/random\_search\_test.cpp](https://app.codecov.io/gh/boostorg/math/pull/1083?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC9yYW5kb21fc2VhcmNoX3Rlc3QuY3Bw) | `100.00% <ø> (ø)` | |  
| [test/test\_functions\_for\_optimization.hpp](https://app.codecov.io/gh/boostorg/math/pull/1083?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC90ZXN0X2Z1bmN0aW9uc19mb3Jfb3B0aW1pemF0aW9uLmhwcA==) | `100.00% <ø> (ø)` | |  
  
... and [1 file with indirect coverage changes](https://app.codecov.io/gh/boostorg/math/pull/1083/indirect-changes?src=pr&el=tree-more&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/math/pull/1083?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/math/pull/1083?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [91f3a22...5ed273d](https://app.codecov.io/gh/boostorg/math/pull/1083?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---
