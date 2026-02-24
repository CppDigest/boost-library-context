# #2828 Use BUILD_TESTING as the default value for Beast_BUILD_TESTS. [Merged]

> Username: JohannesWilde  
> Created at: 2024-02-24 09:08:22 UTC  
> Updated at: 2024-02-25 05:46:20 UTC  
> Merged at: 2024-02-25 05:46:19 UTC  
> Closed at: 2024-02-25 05:46:19 UTC  
> Url: https://github.com/boostorg/beast/pull/2828  

This is the variable most other boost libraries use for initial selection of whether to build their tests [or they default to OFF].  
  
To me this makes usage as a third_party, where I don't necessarily want to run all tests with every build, more consistent.

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2024-02-24 10:05:21 UTC  
> Updated_at: 2024-02-24 10:26:45 UTC  
> Url: https://github.com/boostorg/beast/pull/2828#issuecomment-1962317140  

## [Codecov](https://app.codecov.io/gh/boostorg/beast/pull/2828?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
All modified and coverable lines are covered by tests :white_check_mark:  
> Project coverage is 93.03%. Comparing base [(`075db2f`)](https://app.codecov.io/gh/boostorg/beast/commit/075db2fc370ecbe822ab8c2ead954a88e9c95c18?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) to head [(`1fa295b`)](https://app.codecov.io/gh/boostorg/beast/pull/2828?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/beast/pull/2828/graphs/tree.svg?width=650&height=150&src=pr&token=Gq6MFA9JRF&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/beast/pull/2828?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@             Coverage Diff             @@  
##           develop    #2828      +/-   ##  
===========================================  
- Coverage    93.06%   93.03%   -0.03%       
===========================================  
  Files          178      178                
  Lines        13687    13687                
===========================================  
- Hits         12738    12734       -4       
- Misses         949      953       +4       
```  
  
  
[see 1 file with indirect coverage changes](https://app.codecov.io/gh/boostorg/beast/pull/2828/indirect-changes?src=pr&el=tree-more&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/beast/pull/2828?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/beast/pull/2828?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [075db2f...1fa295b](https://app.codecov.io/gh/boostorg/beast/pull/2828?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---
