# #168 Fix for issue #166 negative precision [Merged]

> Username: mborland  
> Created at: 2024-02-20 09:36:00 UTC  
> Updated at: 2024-02-22 08:03:39 UTC  
> Merged at: 2024-02-22 08:03:36 UTC  
> Closed at: 2024-02-22 08:03:36 UTC  
> Url: https://github.com/boostorg/charconv/pull/168  

Partially Addresses: #166   
  
Adds additional overloads to floating point `to_chars` to change the behavior based on unspecified vs negative precision

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2024-02-20 11:30:20 UTC  
> Updated_at: 2024-02-20 11:39:35 UTC  
> Url: https://github.com/boostorg/charconv/pull/168#issuecomment-1954019284  

## [Codecov](https://app.codecov.io/gh/boostorg/charconv/pull/168?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
Attention: `3 lines` in your changes are missing coverage. Please review.  
> Comparison is base [(`f5e1971`)](https://app.codecov.io/gh/boostorg/charconv/commit/f5e197177b433e3fa35627ce862fab799cfa4812?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) 92.30% compared to head [(`340a72e`)](https://app.codecov.io/gh/boostorg/charconv/pull/168?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) 92.32%.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/charconv/pull/168/graphs/tree.svg?width=650&height=150&src=pr&token=jTnxtbJoTv&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/charconv/pull/168?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #168      +/-   ##  
===========================================  
+ Coverage    92.30%   92.32%   +0.02%       
===========================================  
  Files           64       65       +1       
  Lines         8316     8340      +24       
===========================================  
+ Hits          7676     7700      +24       
  Misses         640      640                
```  
  
  
| [Files](https://app.codecov.io/gh/boostorg/charconv/pull/168?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/charconv/to\_chars.hpp](https://app.codecov.io/gh/boostorg/charconv/pull/168?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9jaGFyY29udi90b19jaGFycy5ocHA=) | `100.00% <ø> (ø)` | |  
| [src/to\_chars.cpp](https://app.codecov.io/gh/boostorg/charconv/pull/168?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-c3JjL3RvX2NoYXJzLmNwcA==) | `93.44% <100.00%> (+0.85%)` | :arrow_up: |  
| [test/github\_issue\_166.cpp](https://app.codecov.io/gh/boostorg/charconv/pull/168?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC9naXRodWJfaXNzdWVfMTY2LmNwcA==) | `100.00% <100.00%> (ø)` | |  
| [src/to\_chars\_float\_impl.hpp](https://app.codecov.io/gh/boostorg/charconv/pull/168?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-c3JjL3RvX2NoYXJzX2Zsb2F0X2ltcGwuaHBw) | `80.86% <89.65%> (+1.30%)` | :arrow_up: |  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/charconv/pull/168?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/charconv/pull/168?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [f5e1971...340a72e](https://app.codecov.io/gh/boostorg/charconv/pull/168?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---
