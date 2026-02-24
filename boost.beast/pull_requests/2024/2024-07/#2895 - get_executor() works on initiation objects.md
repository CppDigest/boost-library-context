# #2895 get_executor() works on initiation objects [Merged]

> Username: ashtum  
> Created at: 2024-07-04 10:40:49 UTC  
> Updated at: 2024-07-04 16:19:42 UTC  
> Merged at: 2024-07-04 16:19:42 UTC  
> Closed at: 2024-07-04 16:19:42 UTC  
> Url: https://github.com/boostorg/beast/pull/2895  

This is required for the new `asio::cancel_at` and `asio::cancel_after` completion token adapters to work.

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2024-07-04 14:56:04 UTC  
> Url: https://github.com/boostorg/beast/pull/2895#issuecomment-2209179252  

## [Codecov](https://app.codecov.io/gh/boostorg/beast/pull/2895?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
Attention: Patch coverage is `58.06452%` with `26 lines` in your changes missing coverage. Please review.  
> Project coverage is 93.09%. Comparing base [(`1a2b85b`)](https://app.codecov.io/gh/boostorg/beast/commit/1a2b85b7a4a5edd6ab5f3d84bdaa50e3b09bc471?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) to head [(`97fc09c`)](https://app.codecov.io/gh/boostorg/beast/commit/97fc09c107f1e4930dbad3191de06540567be1f0?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/beast/pull/2895/graphs/tree.svg?width=650&height=150&src=pr&token=Gq6MFA9JRF&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/beast/pull/2895?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@             Coverage Diff             @@  
##           develop    #2895      +/-   ##  
===========================================  
- Coverage    93.23%   93.09%   -0.14%       
===========================================  
  Files          177      177                
  Lines        13675    13709      +34       
===========================================  
+ Hits         12750    12763      +13       
- Misses         925      946      +21       
```  
  
| [Files](https://app.codecov.io/gh/boostorg/beast/pull/2895?dropdown=coverage&src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [...boost/beast/\_experimental/http/impl/icy\_stream.hpp](https://app.codecov.io/gh/boostorg/beast/pull/2895?src=pr&el=tree&filepath=include%2Fboost%2Fbeast%2F_experimental%2Fhttp%2Fimpl%2Ficy_stream.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9fZXhwZXJpbWVudGFsL2h0dHAvaW1wbC9pY3lfc3RyZWFtLmhwcA==) | `96.03% <100.00%> (+0.03%)` | :arrow_up: |  
| [...ude/boost/beast/\_experimental/test/impl/stream.hpp](https://app.codecov.io/gh/boostorg/beast/pull/2895?src=pr&el=tree&filepath=include%2Fboost%2Fbeast%2F_experimental%2Ftest%2Fimpl%2Fstream.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9fZXhwZXJpbWVudGFsL3Rlc3QvaW1wbC9zdHJlYW0uaHBw) | `99.31% <100.00%> (ø)` | |  
| [include/boost/beast/core/detect\_ssl.hpp](https://app.codecov.io/gh/boostorg/beast/pull/2895?src=pr&el=tree&filepath=include%2Fboost%2Fbeast%2Fcore%2Fdetect_ssl.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9jb3JlL2RldGVjdF9zc2wuaHBw) | `93.93% <100.00%> (+1.51%)` | :arrow_up: |  
| [...ude/boost/beast/core/impl/buffered\_read\_stream.hpp](https://app.codecov.io/gh/boostorg/beast/pull/2895?src=pr&el=tree&filepath=include%2Fboost%2Fbeast%2Fcore%2Fimpl%2Fbuffered_read_stream.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9jb3JlL2ltcGwvYnVmZmVyZWRfcmVhZF9zdHJlYW0uaHBw) | `91.52% <100.00%> (+0.14%)` | :arrow_up: |  
| [include/boost/beast/core/impl/flat\_stream.hpp](https://app.codecov.io/gh/boostorg/beast/pull/2895?src=pr&el=tree&filepath=include%2Fboost%2Fbeast%2Fcore%2Fimpl%2Fflat_stream.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9jb3JlL2ltcGwvZmxhdF9zdHJlYW0uaHBw) | `100.00% <100.00%> (ø)` | |  
| [include/boost/beast/http/impl/read.hpp](https://app.codecov.io/gh/boostorg/beast/pull/2895?src=pr&el=tree&filepath=include%2Fboost%2Fbeast%2Fhttp%2Fimpl%2Fread.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9odHRwL2ltcGwvcmVhZC5ocHA=) | `98.46% <100.00%> (+<0.01%)` | :arrow_up: |  
| [include/boost/beast/http/impl/write.hpp](https://app.codecov.io/gh/boostorg/beast/pull/2895?src=pr&el=tree&filepath=include%2Fboost%2Fbeast%2Fhttp%2Fimpl%2Fwrite.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9odHRwL2ltcGwvd3JpdGUuaHBw) | `92.07% <100.00%> (+1.71%)` | :arrow_up: |  
| [include/boost/beast/websocket/impl/close.hpp](https://app.codecov.io/gh/boostorg/beast/pull/2895?src=pr&el=tree&filepath=include%2Fboost%2Fbeast%2Fwebsocket%2Fimpl%2Fclose.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9iZWFzdC93ZWJzb2NrZXQvaW1wbC9jbG9zZS5ocHA=) | `95.97% <33.33%> (-0.98%)` | :arrow_down: |  
| [include/boost/beast/websocket/impl/handshake.hpp](https://app.codecov.io/gh/boostorg/beast/pull/2895?src=pr&el=tree&filepath=include%2Fboost%2Fbeast%2Fwebsocket%2Fimpl%2Fhandshake.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9iZWFzdC93ZWJzb2NrZXQvaW1wbC9oYW5kc2hha2UuaHBw) | `95.62% <60.00%> (-1.42%)` | :arrow_down: |  
| [include/boost/beast/websocket/impl/ping.hpp](https://app.codecov.io/gh/boostorg/beast/pull/2895?src=pr&el=tree&filepath=include%2Fboost%2Fbeast%2Fwebsocket%2Fimpl%2Fping.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9iZWFzdC93ZWJzb2NrZXQvaW1wbC9waW5nLmhwcA==) | `93.65% <50.00%> (-1.52%)` | :arrow_down: |  
| ... and [4 more](https://app.codecov.io/gh/boostorg/beast/pull/2895?src=pr&el=tree-more&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | |  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/beast/pull/2895?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/beast/pull/2895?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [1a2b85b...97fc09c](https://app.codecov.io/gh/boostorg/beast/pull/2895?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---
