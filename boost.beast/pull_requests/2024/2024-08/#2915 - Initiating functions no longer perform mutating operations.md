# #2915 Initiating functions no longer perform mutating operations [Merged]

> Username: ashtum  
> Created at: 2024-08-05 16:21:33 UTC  
> Updated at: 2024-08-06 12:12:40 UTC  
> Merged at: 2024-08-06 12:12:40 UTC  
> Closed at: 2024-08-06 12:12:40 UTC  
> Url: https://github.com/boostorg/beast/pull/2915  

Using `asio::deferred` as a completion token defers the initiation of operations and creates a lazy operation which might even be discarded. We need to move mutating operations to the initiator function objects or the constructor of operation objects, which are guaranteed to run when the deferred operation is launched.

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2024-08-05 23:11:14 UTC  
> Updated_at: 2024-08-06 09:06:02 UTC  
> Url: https://github.com/boostorg/beast/pull/2915#issuecomment-2270071301  

## [Codecov](https://app.codecov.io/gh/boostorg/beast/pull/2915?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
Attention: Patch coverage is `80.00000%` with `2 lines` in your changes missing coverage. Please review.  
> Project coverage is 93.04%. Comparing base [(`fee9be0`)](https://app.codecov.io/gh/boostorg/beast/commit/fee9be0be10c9c9a22ac1505a710d1d8ed5a3dfb?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) to head [(`eff1afb`)](https://app.codecov.io/gh/boostorg/beast/commit/eff1afb8fc7f1f22fa3c6415bc2c45c3aca24655?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
| [Files](https://app.codecov.io/gh/boostorg/beast/pull/2915?dropdown=coverage&src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Patch % | Lines |  
|---|---|---|  
| [include/boost/beast/http/impl/write.hpp](https://app.codecov.io/gh/boostorg/beast/pull/2915?src=pr&el=tree&filepath=include%2Fboost%2Fbeast%2Fhttp%2Fimpl%2Fwrite.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9odHRwL2ltcGwvd3JpdGUuaHBw) | 66.66% | [2 Missing :warning: ](https://app.codecov.io/gh/boostorg/beast/pull/2915?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) |  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/beast/pull/2915/graphs/tree.svg?width=650&height=150&src=pr&token=Gq6MFA9JRF&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/beast/pull/2915?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@             Coverage Diff             @@  
##           develop    #2915      +/-   ##  
===========================================  
- Coverage    93.09%   93.04%   -0.06%       
===========================================  
  Files          177      177                
  Lines        13709    13709                
===========================================  
- Hits         12763    12755       -8       
- Misses         946      954       +8       
```  
  
| [Files](https://app.codecov.io/gh/boostorg/beast/pull/2915?dropdown=coverage&src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/beast/websocket/impl/accept.hpp](https://app.codecov.io/gh/boostorg/beast/pull/2915?src=pr&el=tree&filepath=include%2Fboost%2Fbeast%2Fwebsocket%2Fimpl%2Faccept.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9iZWFzdC93ZWJzb2NrZXQvaW1wbC9hY2NlcHQuaHBw) | `96.04% <100.00%> (-0.04%)` | :arrow_down: |  
| [include/boost/beast/websocket/impl/handshake.hpp](https://app.codecov.io/gh/boostorg/beast/pull/2915?src=pr&el=tree&filepath=include%2Fboost%2Fbeast%2Fwebsocket%2Fimpl%2Fhandshake.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9iZWFzdC93ZWJzb2NrZXQvaW1wbC9oYW5kc2hha2UuaHBw) | `95.65% <100.00%> (+0.03%)` | :arrow_up: |  
| [include/boost/beast/websocket/impl/teardown.hpp](https://app.codecov.io/gh/boostorg/beast/pull/2915?src=pr&el=tree&filepath=include%2Fboost%2Fbeast%2Fwebsocket%2Fimpl%2Fteardown.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9iZWFzdC93ZWJzb2NrZXQvaW1wbC90ZWFyZG93bi5ocHA=) | `78.26% <100.00%> (ø)` | |  
| [include/boost/beast/http/impl/write.hpp](https://app.codecov.io/gh/boostorg/beast/pull/2915?src=pr&el=tree&filepath=include%2Fboost%2Fbeast%2Fhttp%2Fimpl%2Fwrite.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9odHRwL2ltcGwvd3JpdGUuaHBw) | `90.35% <66.66%> (-1.72%)` | :arrow_down: |  
  
... and [1 file with indirect coverage changes](https://app.codecov.io/gh/boostorg/beast/pull/2915/indirect-changes?src=pr&el=tree-more&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/beast/pull/2915?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/beast/pull/2915?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [fee9be0...eff1afb](https://app.codecov.io/gh/boostorg/beast/pull/2915?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---
