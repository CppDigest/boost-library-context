# #410 Connections now check for in-progress async operations and fail without UB [Merged]

> Username: anarthal  
> Created at: 2025-02-05 14:26:26 UTC  
> Updated at: 2025-02-05 19:16:10 UTC  
> Merged at: 2025-02-05 19:15:54 UTC  
> Closed at: 2025-02-05 19:15:54 UTC  
> Url: https://github.com/boostorg/mysql/pull/410  

connection and any_connection now check whether there is an in-progress async operation, and fail with client_errc::operation_in_progress if there is one. This situation no longer triggers undefined behavior.  
Refactored the internal sans-io algorithms  
  
close #405

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2025-02-05 19:09:08 UTC  
> Updated_at: 2025-02-05 19:16:10 UTC  
> Url: https://github.com/boostorg/mysql/pull/410#issuecomment-2637800592  

## [Codecov](https://app.codecov.io/gh/boostorg/mysql/pull/410?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
All modified and coverable lines are covered by tests :white_check_mark:  
> Project coverage is 98.95%. Comparing base [(`91cd262`)](https://app.codecov.io/gh/boostorg/mysql/commit/91cd262c68b5d855de5d67d3986e17155fa0feef?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) to head [(`eb97504`)](https://app.codecov.io/gh/boostorg/mysql/commit/eb97504a75ad56ff6347dfa1142b28b324ed4cf4?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> Report is 1 commits behind head on develop.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/mysql/pull/410/graphs/tree.svg?width=650&height=150&src=pr&token=ajbbjm3OiO&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/mysql/pull/410?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #410      +/-   ##  
===========================================  
- Coverage    98.95%   98.95%   -0.01%       
===========================================  
  Files          142      142                
  Lines         7207     7188      -19       
===========================================  
- Hits          7132     7113      -19       
  Misses          75       75                
```  
  
| [Files with missing lines](https://app.codecov.io/gh/boostorg/mysql/pull/410?dropdown=coverage&src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/mysql/any\_connection.hpp](https://app.codecov.io/gh/boostorg/mysql/pull/410?src=pr&el=tree&filepath=include%2Fboost%2Fmysql%2Fany_connection.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9teXNxbC9hbnlfY29ubmVjdGlvbi5ocHA=) | `100.00% <ø> (ø)` | |  
| [include/boost/mysql/client\_errc.hpp](https://app.codecov.io/gh/boostorg/mysql/pull/410?src=pr&el=tree&filepath=include%2Fboost%2Fmysql%2Fclient_errc.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9teXNxbC9jbGllbnRfZXJyYy5ocHA=) | `100.00% <ø> (ø)` | |  
| [include/boost/mysql/connection.hpp](https://app.codecov.io/gh/boostorg/mysql/pull/410?src=pr&el=tree&filepath=include%2Fboost%2Fmysql%2Fconnection.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9teXNxbC9jb25uZWN0aW9uLmhwcA==) | `97.82% <ø> (ø)` | |  
| [include/boost/mysql/detail/any\_resumable\_ref.hpp](https://app.codecov.io/gh/boostorg/mysql/pull/410?src=pr&el=tree&filepath=include%2Fboost%2Fmysql%2Fdetail%2Fany_resumable_ref.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9teXNxbC9kZXRhaWwvYW55X3Jlc3VtYWJsZV9yZWYuaHBw) | `100.00% <100.00%> (ø)` | |  
| [include/boost/mysql/detail/connection\_impl.hpp](https://app.codecov.io/gh/boostorg/mysql/pull/410?src=pr&el=tree&filepath=include%2Fboost%2Fmysql%2Fdetail%2Fconnection_impl.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9teXNxbC9kZXRhaWwvY29ubmVjdGlvbl9pbXBsLmhwcA==) | `94.82% <100.00%> (-0.14%)` | :arrow_down: |  
| [include/boost/mysql/detail/engine.hpp](https://app.codecov.io/gh/boostorg/mysql/pull/410?src=pr&el=tree&filepath=include%2Fboost%2Fmysql%2Fdetail%2Fengine.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9teXNxbC9kZXRhaWwvZW5naW5lLmhwcA==) | `100.00% <ø> (ø)` | |  
| [include/boost/mysql/detail/engine\_impl.hpp](https://app.codecov.io/gh/boostorg/mysql/pull/410?src=pr&el=tree&filepath=include%2Fboost%2Fmysql%2Fdetail%2Fengine_impl.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9teXNxbC9kZXRhaWwvZW5naW5lX2ltcGwuaHBw) | `96.96% <100.00%> (-0.05%)` | :arrow_down: |  
| [...clude/boost/mysql/detail/engine\_stream\_adaptor.hpp](https://app.codecov.io/gh/boostorg/mysql/pull/410?src=pr&el=tree&filepath=include%2Fboost%2Fmysql%2Fdetail%2Fengine_stream_adaptor.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9teXNxbC9kZXRhaWwvZW5naW5lX3N0cmVhbV9hZGFwdG9yLmhwcA==) | `100.00% <100.00%> (ø)` | |  
| [include/boost/mysql/detail/next\_action.hpp](https://app.codecov.io/gh/boostorg/mysql/pull/410?src=pr&el=tree&filepath=include%2Fboost%2Fmysql%2Fdetail%2Fnext_action.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9teXNxbC9kZXRhaWwvbmV4dF9hY3Rpb24uaHBw) | `100.00% <100.00%> (ø)` | |  
| [include/boost/mysql/impl/error\_categories.ipp](https://app.codecov.io/gh/boostorg/mysql/pull/410?src=pr&el=tree&filepath=include%2Fboost%2Fmysql%2Fimpl%2Ferror_categories.ipp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9teXNxbC9pbXBsL2Vycm9yX2NhdGVnb3JpZXMuaXBw) | `98.64% <100.00%> (+0.03%)` | :arrow_up: |  
| ... and [18 more](https://app.codecov.io/gh/boostorg/mysql/pull/410?src=pr&el=tree-more&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | |  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/mysql/pull/410?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/mysql/pull/410?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [91cd262...eb97504](https://app.codecov.io/gh/boostorg/mysql/pull/410?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---
