# #3029 Fix websocket permessage-deflate error on partial message consumption [Merged]

> Username: ashtum  
> Created at: 2025-09-02 18:43:14 UTC  
> Updated at: 2025-09-04 04:45:24 UTC  
> Merged at: 2025-09-04 04:45:23 UTC  
> Closed at: 2025-09-04 04:45:23 UTC  
> Url: https://github.com/boostorg/beast/pull/3029  

fixes #3028

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2025-09-03 13:51:16 UTC  
> Url: https://github.com/boostorg/beast/pull/3029#issuecomment-3249350443  

## [Codecov](https://app.codecov.io/gh/boostorg/beast/pull/3029?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
:x: Patch coverage is `94.44444%` with `2 lines` in your changes missing coverage. Please review.  
:white_check_mark: Project coverage is 93.16%. Comparing base ([`9b24b28`](https://app.codecov.io/gh/boostorg/beast/commit/9b24b28d502946583300d3e58767cb30a8ec8486?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)) to head ([`0a4d671`](https://app.codecov.io/gh/boostorg/beast/commit/0a4d67139d285768842057bc68db61c0c1f9c7f0?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)).  
  
| [Files with missing lines](https://app.codecov.io/gh/boostorg/beast/pull/3029?dropdown=coverage&src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Patch % | Lines |  
|---|---|---|  
| [include/boost/beast/websocket/detail/impl\_base.hpp](https://app.codecov.io/gh/boostorg/beast/pull/3029?src=pr&el=tree&filepath=include%2Fboost%2Fbeast%2Fwebsocket%2Fdetail%2Fimpl_base.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9iZWFzdC93ZWJzb2NrZXQvZGV0YWlsL2ltcGxfYmFzZS5ocHA=) | 83.33% | [2 Missing :warning: ](https://app.codecov.io/gh/boostorg/beast/pull/3029?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) |  
  
<details><summary>Additional details and impacted files</summary>  
  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/beast/pull/3029/graphs/tree.svg?width=650&height=150&src=pr&token=Gq6MFA9JRF&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/beast/pull/3029?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@             Coverage Diff             @@  
##           develop    #3029      +/-   ##  
===========================================  
+ Coverage    93.09%   93.16%   +0.07%       
===========================================  
  Files          176      176                
  Lines        13647    13655       +8       
===========================================  
+ Hits         12704    12722      +18       
+ Misses         943      933      -10       
```  
  
| [Files with missing lines](https://app.codecov.io/gh/boostorg/beast/pull/3029?dropdown=coverage&src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/beast/websocket/impl/read.hpp](https://app.codecov.io/gh/boostorg/beast/pull/3029?src=pr&el=tree&filepath=include%2Fboost%2Fbeast%2Fwebsocket%2Fimpl%2Fread.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9iZWFzdC93ZWJzb2NrZXQvaW1wbC9yZWFkLmhwcA==) | `95.26% <100.00%> (+0.59%)` | :arrow_up: |  
| [include/boost/beast/websocket/detail/impl\_base.hpp](https://app.codecov.io/gh/boostorg/beast/pull/3029?src=pr&el=tree&filepath=include%2Fboost%2Fbeast%2Fwebsocket%2Fdetail%2Fimpl_base.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9iZWFzdC93ZWJzb2NrZXQvZGV0YWlsL2ltcGxfYmFzZS5ocHA=) | `86.25% <83.33%> (-0.18%)` | :arrow_down: |  
  
... and [1 file with indirect coverage changes](https://app.codecov.io/gh/boostorg/beast/pull/3029/indirect-changes?src=pr&el=tree-more&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/beast/pull/3029?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/beast/pull/3029?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [9b24b28...0a4d671](https://app.codecov.io/gh/boostorg/beast/pull/3029?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
</details>  
<details><summary> :rocket: New features to boost your workflow: </summary>  
  
- :snowflake: [Test Analytics](https://docs.codecov.com/docs/test-analytics): Detect flaky tests, report on failures, and find test suite problems.  
</details>

---
