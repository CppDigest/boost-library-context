# #2881 read_size_hint does not exceed read_message_max [Merged]

> Username: ashtum  
> Created at: 2024-06-05 13:04:18 UTC  
> Updated at: 2024-06-05 21:19:08 UTC  
> Merged at: 2024-06-05 21:19:08 UTC  
> Closed at: 2024-06-05 21:19:08 UTC  
> Url: https://github.com/boostorg/beast/pull/2881  

Fixes #2879

---

## Comment 1

> Username: vinniefalco  
> Created_at: 2024-06-05 13:39:36 UTC  
> Url: https://github.com/boostorg/beast/pull/2881#issuecomment-2149973872  

Good commit message :)

---

## Comment 2

> Username: codecov[bot]  
> Created_at: 2024-06-05 15:33:44 UTC  
> Updated_at: 2024-06-05 19:01:32 UTC  
> Url: https://github.com/boostorg/beast/pull/2881#issuecomment-2150370472  

## [Codecov](https://app.codecov.io/gh/boostorg/beast/pull/2881?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
Attention: Patch coverage is `85.71429%` with `1 line` in your changes missing coverage. Please review.  
> Project coverage is 93.20%. Comparing base [(`4bff457`)](https://app.codecov.io/gh/boostorg/beast/commit/4bff457ef7064c3288905d23bc728b70acc7ede2?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) to head [(`412c5d8`)](https://app.codecov.io/gh/boostorg/beast/commit/412c5d87b52aa4e774ffbb06685c55575a11406d?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/beast/pull/2881/graphs/tree.svg?width=650&height=150&src=pr&token=Gq6MFA9JRF&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/beast/pull/2881?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@           Coverage Diff            @@  
##           develop    #2881   +/-   ##  
========================================  
  Coverage    93.20%   93.20%             
========================================  
  Files          177      177             
  Lines        13671    13673    +2       
========================================  
+ Hits         12742    12744    +2       
  Misses         929      929             
```  
  
  
| [Files](https://app.codecov.io/gh/boostorg/beast/pull/2881?dropdown=coverage&src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/beast/websocket/detail/impl\_base.hpp](https://app.codecov.io/gh/boostorg/beast/pull/2881?src=pr&el=tree&filepath=include%2Fboost%2Fbeast%2Fwebsocket%2Fdetail%2Fimpl_base.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9iZWFzdC93ZWJzb2NrZXQvZGV0YWlsL2ltcGxfYmFzZS5ocHA=) | `86.01% <100.00%> (+0.29%)` | :arrow_up: |  
| [include/boost/beast/websocket/impl/read.hpp](https://app.codecov.io/gh/boostorg/beast/pull/2881?src=pr&el=tree&filepath=include%2Fboost%2Fbeast%2Fwebsocket%2Fimpl%2Fread.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9iZWFzdC93ZWJzb2NrZXQvaW1wbC9yZWFkLmhwcA==) | `95.24% <100.00%> (ø)` | |  
| [include/boost/beast/websocket/impl/stream\_impl.hpp](https://app.codecov.io/gh/boostorg/beast/pull/2881?src=pr&el=tree&filepath=include%2Fboost%2Fbeast%2Fwebsocket%2Fimpl%2Fstream_impl.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9iZWFzdC93ZWJzb2NrZXQvaW1wbC9zdHJlYW1faW1wbC5ocHA=) | `96.19% <100.00%> (ø)` | |  
| [include/boost/beast/websocket/impl/stream.hpp](https://app.codecov.io/gh/boostorg/beast/pull/2881?src=pr&el=tree&filepath=include%2Fboost%2Fbeast%2Fwebsocket%2Fimpl%2Fstream.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9iZWFzdC93ZWJzb2NrZXQvaW1wbC9zdHJlYW0uaHBw) | `73.52% <0.00%> (-0.51%)` | :arrow_down: |  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/beast/pull/2881?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/beast/pull/2881?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [4bff457...412c5d8](https://app.codecov.io/gh/boostorg/beast/pull/2881?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---
