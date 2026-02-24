# #2818 Update host string after SSL_set_tlsext_host_name [Merged]

> Username: ashtum  
> Created at: 2024-02-14 15:22:56 UTC  
> Updated at: 2024-02-14 16:33:10 UTC  
> Merged at: 2024-02-14 16:33:10 UTC  
> Closed at: 2024-02-14 16:33:10 UTC  
> Url: https://github.com/boostorg/beast/pull/2818  

`host_` string should be updated after its usage in SSL_set_tlsext_host_name, otherwise this would cause an SSL handshake error.

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2024-02-14 16:21:38 UTC  
> Url: https://github.com/boostorg/beast/pull/2818#issuecomment-1944167701  

## [Codecov](https://app.codecov.io/gh/boostorg/beast/pull/2818?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
All modified and coverable lines are covered by tests :white_check_mark:  
> Comparison is base [(`40c22cb`)](https://app.codecov.io/gh/boostorg/beast/commit/40c22cbfa7aea25386f6f13967bbdd17da00657a?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) 93.06% compared to head [(`0bb892c`)](https://app.codecov.io/gh/boostorg/beast/pull/2818?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) 93.03%.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/beast/pull/2818/graphs/tree.svg?width=650&height=150&src=pr&token=Gq6MFA9JRF&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/beast/pull/2818?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@             Coverage Diff             @@  
##           develop    #2818      +/-   ##  
===========================================  
- Coverage    93.06%   93.03%   -0.03%       
===========================================  
  Files          178      178                
  Lines        13687    13687                
===========================================  
- Hits         12738    12734       -4       
- Misses         949      953       +4       
```  
  
  
[see 1 file with indirect coverage changes](https://app.codecov.io/gh/boostorg/beast/pull/2818/indirect-changes?src=pr&el=tree-more&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/beast/pull/2818?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/beast/pull/2818?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [40c22cb...0bb892c](https://app.codecov.io/gh/boostorg/beast/pull/2818?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---
