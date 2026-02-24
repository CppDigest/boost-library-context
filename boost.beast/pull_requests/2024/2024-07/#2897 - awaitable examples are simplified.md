# #2897 awaitable examples are simplified [Merged]

> Username: ashtum  
> Created at: 2024-07-05 12:55:23 UTC  
> Updated at: 2024-07-07 11:28:49 UTC  
> Merged at: 2024-07-07 11:28:49 UTC  
> Closed at: 2024-07-07 11:28:49 UTC  
> Url: https://github.com/boostorg/beast/pull/2897  

No need for `rebind_executor` as `asio::deferred` is now the default completion token.

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2024-07-05 13:59:10 UTC  
> Updated_at: 2024-07-06 20:12:03 UTC  
> Url: https://github.com/boostorg/beast/pull/2897#issuecomment-2210932243  

## [Codecov](https://app.codecov.io/gh/boostorg/beast/pull/2897?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
All modified and coverable lines are covered by tests :white_check_mark:  
> Project coverage is 93.09%. Comparing base [(`a3a800b`)](https://app.codecov.io/gh/boostorg/beast/commit/a3a800b2e3d25c1d9c4ddab1faa7886175c2bb54?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) to head [(`4072be7`)](https://app.codecov.io/gh/boostorg/beast/commit/4072be7415655031c26722ceda3f6523bd339bb9?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/beast/pull/2897/graphs/tree.svg?width=650&height=150&src=pr&token=Gq6MFA9JRF&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/beast/pull/2897?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@           Coverage Diff            @@  
##           develop    #2897   +/-   ##  
========================================  
  Coverage    93.09%   93.09%             
========================================  
  Files          177      177             
  Lines        13709    13709             
========================================  
  Hits         12763    12763             
  Misses         946      946             
```  
  
| [Files](https://app.codecov.io/gh/boostorg/beast/pull/2897?dropdown=coverage&src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [...ude/boost/beast/\_experimental/test/impl/stream.hpp](https://app.codecov.io/gh/boostorg/beast/pull/2897?src=pr&el=tree&filepath=include%2Fboost%2Fbeast%2F_experimental%2Ftest%2Fimpl%2Fstream.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9fZXhwZXJpbWVudGFsL3Rlc3QvaW1wbC9zdHJlYW0uaHBw) | `99.31% <100.00%> (ø)` | |  
| [include/boost/beast/core/async\_base.hpp](https://app.codecov.io/gh/boostorg/beast/pull/2897?src=pr&el=tree&filepath=include%2Fboost%2Fbeast%2Fcore%2Fasync_base.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9jb3JlL2FzeW5jX2Jhc2UuaHBw) | `100.00% <100.00%> (ø)` | |  
| [include/boost/beast/core/impl/basic\_stream.hpp](https://app.codecov.io/gh/boostorg/beast/pull/2897?src=pr&el=tree&filepath=include%2Fboost%2Fbeast%2Fcore%2Fimpl%2Fbasic_stream.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9jb3JlL2ltcGwvYmFzaWNfc3RyZWFtLmhwcA==) | `83.06% <100.00%> (ø)` | |  
| [...ude/boost/beast/core/impl/buffered\_read\_stream.hpp](https://app.codecov.io/gh/boostorg/beast/pull/2897?src=pr&el=tree&filepath=include%2Fboost%2Fbeast%2Fcore%2Fimpl%2Fbuffered_read_stream.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9jb3JlL2ltcGwvYnVmZmVyZWRfcmVhZF9zdHJlYW0uaHBw) | `91.52% <100.00%> (ø)` | |  
| [include/boost/beast/http/impl/read.hpp](https://app.codecov.io/gh/boostorg/beast/pull/2897?src=pr&el=tree&filepath=include%2Fboost%2Fbeast%2Fhttp%2Fimpl%2Fread.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9odHRwL2ltcGwvcmVhZC5ocHA=) | `98.46% <100.00%> (ø)` | |  
| [include/boost/beast/http/impl/write.hpp](https://app.codecov.io/gh/boostorg/beast/pull/2897?src=pr&el=tree&filepath=include%2Fboost%2Fbeast%2Fhttp%2Fimpl%2Fwrite.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9odHRwL2ltcGwvd3JpdGUuaHBw) | `92.07% <100.00%> (ø)` | |  
| [include/boost/beast/websocket/detail/impl\_base.hpp](https://app.codecov.io/gh/boostorg/beast/pull/2897?src=pr&el=tree&filepath=include%2Fboost%2Fbeast%2Fwebsocket%2Fdetail%2Fimpl_base.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9iZWFzdC93ZWJzb2NrZXQvZGV0YWlsL2ltcGxfYmFzZS5ocHA=) | `86.01% <ø> (ø)` | |  
| [include/boost/beast/websocket/impl/teardown.hpp](https://app.codecov.io/gh/boostorg/beast/pull/2897?src=pr&el=tree&filepath=include%2Fboost%2Fbeast%2Fwebsocket%2Fimpl%2Fteardown.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9iZWFzdC93ZWJzb2NrZXQvaW1wbC90ZWFyZG93bi5ocHA=) | `78.26% <100.00%> (ø)` | |  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/beast/pull/2897?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/beast/pull/2897?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [a3a800b...4072be7](https://app.codecov.io/gh/boostorg/beast/pull/2897?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---
