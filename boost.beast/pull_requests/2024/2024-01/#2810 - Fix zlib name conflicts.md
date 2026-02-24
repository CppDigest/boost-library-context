# #2810 Fix zlib name conflicts [Merged]

> Username: ashtum  
> Created at: 2024-01-31 11:39:19 UTC  
> Updated at: 2024-01-31 14:23:20 UTC  
> Merged at: 2024-01-31 14:23:19 UTC  
> Closed at: 2024-01-31 14:23:19 UTC  
> Url: https://github.com/boostorg/beast/pull/2810  

Fixes #2809

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2024-01-31 12:45:27 UTC  
> Updated_at: 2024-01-31 13:00:08 UTC  
> Url: https://github.com/boostorg/beast/pull/2810#issuecomment-1919037363  

## [Codecov](https://app.codecov.io/gh/boostorg/beast/pull/2810?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
All modified and coverable lines are covered by tests :white_check_mark:  
> Comparison is base [(`5f088b7`)](https://app.codecov.io/gh/boostorg/beast/commit/5f088b72ca92a27662476e5ec9b7a8ff777439c3?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) 93.01% compared to head [(`a69aee6`)](https://app.codecov.io/gh/boostorg/beast/pull/2810?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) 93.04%.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/beast/pull/2810/graphs/tree.svg?width=650&height=150&src=pr&token=Gq6MFA9JRF&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/beast/pull/2810?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@             Coverage Diff             @@  
##           develop    #2810      +/-   ##  
===========================================  
+ Coverage    93.01%   93.04%   +0.02%       
===========================================  
  Files          178      178                
  Lines        13688    13688                
===========================================  
+ Hits         12732    12736       +4       
+ Misses         956      952       -4       
```  
  
  
| [Files](https://app.codecov.io/gh/boostorg/beast/pull/2810?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/beast/zlib/deflate\_stream.hpp](https://app.codecov.io/gh/boostorg/beast/pull/2810?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9iZWFzdC96bGliL2RlZmxhdGVfc3RyZWFtLmhwcA==) | `100.00% <100.00%> (ø)` | |  
| [include/boost/beast/zlib/detail/deflate\_stream.hpp](https://app.codecov.io/gh/boostorg/beast/pull/2810?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9iZWFzdC96bGliL2RldGFpbC9kZWZsYXRlX3N0cmVhbS5ocHA=) | `100.00% <ø> (ø)` | |  
| [include/boost/beast/zlib/detail/deflate\_stream.ipp](https://app.codecov.io/gh/boostorg/beast/pull/2810?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9iZWFzdC96bGliL2RldGFpbC9kZWZsYXRlX3N0cmVhbS5pcHA=) | `88.79% <100.00%> (ø)` | |  
  
... and [1 file with indirect coverage changes](https://app.codecov.io/gh/boostorg/beast/pull/2810/indirect-changes?src=pr&el=tree-more&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/beast/pull/2810?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/beast/pull/2810?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [5f088b7...a69aee6](https://app.codecov.io/gh/boostorg/beast/pull/2810?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---
