# #2882 Partial writes don't cause bytes_transferred underflow [Merged]

> Username: ashtum  
> Created at: 2024-06-06 10:02:31 UTC  
> Updated at: 2024-06-06 14:28:26 UTC  
> Merged at: 2024-06-06 14:28:25 UTC  
> Closed at: 2024-06-06 14:28:25 UTC  
> Url: https://github.com/boostorg/beast/pull/2882  

Fixes #2880

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2024-06-06 11:12:13 UTC  
> Updated_at: 2024-06-06 11:24:32 UTC  
> Url: https://github.com/boostorg/beast/pull/2882#issuecomment-2152066451  

## [Codecov](https://app.codecov.io/gh/boostorg/beast/pull/2882?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
All modified and coverable lines are covered by tests :white_check_mark:  
> Project coverage is 93.20%. Comparing base [(`767397e`)](https://app.codecov.io/gh/boostorg/beast/commit/767397e0c1a761757e88c353c7850ea001dbcfdf?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) to head [(`408fefa`)](https://app.codecov.io/gh/boostorg/beast/commit/408fefa711ebafeb72b1221f728e7e8621100d7a?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/beast/pull/2882/graphs/tree.svg?width=650&height=150&src=pr&token=Gq6MFA9JRF&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/beast/pull/2882?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@             Coverage Diff             @@  
##           develop    #2882      +/-   ##  
===========================================  
- Coverage    93.23%   93.20%   -0.03%       
===========================================  
  Files          177      177                
  Lines        13673    13675       +2       
===========================================  
- Hits         12748    12746       -2       
- Misses         925      929       +4       
```  
  
  
| [Files](https://app.codecov.io/gh/boostorg/beast/pull/2882?dropdown=coverage&src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/beast/websocket/impl/write.hpp](https://app.codecov.io/gh/boostorg/beast/pull/2882?src=pr&el=tree&filepath=include%2Fboost%2Fbeast%2Fwebsocket%2Fimpl%2Fwrite.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9iZWFzdC93ZWJzb2NrZXQvaW1wbC93cml0ZS5ocHA=) | `98.30% <100.00%> (+<0.01%)` | :arrow_up: |  
  
... and [1 file with indirect coverage changes](https://app.codecov.io/gh/boostorg/beast/pull/2882/indirect-changes?src=pr&el=tree-more&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/beast/pull/2882?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/beast/pull/2882?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [767397e...408fefa](https://app.codecov.io/gh/boostorg/beast/pull/2882?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---
