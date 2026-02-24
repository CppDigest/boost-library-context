# #605 Fix up default fmod to perform post-condition checking. [Merged]

> Username: jzmaddock  
> Created at: 2024-02-29 19:19:14 UTC  
> Updated at: 2024-03-01 16:49:48 UTC  
> Merged at: 2024-03-01 13:11:33 UTC  
> Closed at: 2024-03-01 13:11:33 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/605  

Fixes https://github.com/boostorg/multiprecision/issues/604.

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2024-02-29 21:14:59 UTC  
> Updated_at: 2024-02-29 21:27:50 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/605#issuecomment-1971973570  

## [Codecov](https://app.codecov.io/gh/boostorg/multiprecision/pull/605?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
All modified and coverable lines are covered by tests :white_check_mark:  
> Project coverage is 94.1%. Comparing base [(`f0319ec`)](https://app.codecov.io/gh/boostorg/multiprecision/commit/f0319ec047464981d1e2b64864f909b37aaf295e?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) to head [(`062503f`)](https://app.codecov.io/gh/boostorg/multiprecision/pull/605?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/multiprecision/pull/605/graphs/tree.svg?width=650&height=150&src=pr&token=SDDBym7Pc9&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/multiprecision/pull/605?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@            Coverage Diff            @@  
##           develop    #605     +/-   ##  
=========================================  
+ Coverage     94.1%   94.1%   +0.1%       
=========================================  
  Files          273     274      +1       
  Lines        28525   28563     +38       
=========================================  
+ Hits         26834   26874     +40       
+ Misses        1691    1689      -2       
```  
  
  
| [Files](https://app.codecov.io/gh/boostorg/multiprecision/pull/605?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [...nclude/boost/multiprecision/detail/default\_ops.hpp](https://app.codecov.io/gh/boostorg/multiprecision/pull/605?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9tdWx0aXByZWNpc2lvbi9kZXRhaWwvZGVmYXVsdF9vcHMuaHBw) | `90.9% <100.0%> (+0.1%)` | :arrow_up: |  
| [test/git\_issue\_604.cpp](https://app.codecov.io/gh/boostorg/multiprecision/pull/605?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC9naXRfaXNzdWVfNjA0LmNwcA==) | `100.0% <100.0%> (ø)` | |  
  
... and [1 file with indirect coverage changes](https://app.codecov.io/gh/boostorg/multiprecision/pull/605/indirect-changes?src=pr&el=tree-more&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/multiprecision/pull/605?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/multiprecision/pull/605?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [f0319ec...062503f](https://app.codecov.io/gh/boostorg/multiprecision/pull/605?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---

## Comment 2

> Username: jzmaddock  
> Created_at: 2024-03-01 13:11:27 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/605#issuecomment-1973173081  

Failures are unrelated and are being worked on elsewhere... merging.

---
