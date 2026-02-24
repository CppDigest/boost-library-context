# #3005 Conditionally define `immediate_executor_type` in `async_base` [Merged]

> Username: ashtum  
> Created at: 2025-04-25 14:51:40 UTC  
> Updated at: 2025-04-27 12:38:13 UTC  
> Merged at: 2025-04-27 12:38:06 UTC  
> Closed at: 2025-04-27 12:38:06 UTC  
> Url: https://github.com/boostorg/beast/pull/3005  

Fixes #3002

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2025-04-25 16:08:42 UTC  
> Updated_at: 2025-04-27 12:38:12 UTC  
> Url: https://github.com/boostorg/beast/pull/3005#issuecomment-2830833350  

## [Codecov](https://app.codecov.io/gh/boostorg/beast/pull/3005?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
All modified and coverable lines are covered by tests :white_check_mark:  
> Project coverage is 93.04%. Comparing base [(`c5d20f5`)](https://app.codecov.io/gh/boostorg/beast/commit/c5d20f50c5ca162e86411ae98c2ec9a127e6d005?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) to head [(`ba22752`)](https://app.codecov.io/gh/boostorg/beast/commit/ba2275239d17f50434e4a98dec00a58e61037562?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> Report is 3 commits behind head on develop.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/beast/pull/3005/graphs/tree.svg?width=650&height=150&src=pr&token=Gq6MFA9JRF&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/beast/pull/3005?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@             Coverage Diff             @@  
##           develop    #3005      +/-   ##  
===========================================  
- Coverage    93.13%   93.04%   -0.09%       
===========================================  
  Files          177      176       -1       
  Lines        13656    13606      -50       
===========================================  
- Hits         12718    12660      -58       
- Misses         938      946       +8       
```  
  
| [Files with missing lines](https://app.codecov.io/gh/boostorg/beast/pull/3005?dropdown=coverage&src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [...ude/boost/beast/\_experimental/test/impl/stream.hpp](https://app.codecov.io/gh/boostorg/beast/pull/3005?src=pr&el=tree&filepath=include%2Fboost%2Fbeast%2F_experimental%2Ftest%2Fimpl%2Fstream.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9fZXhwZXJpbWVudGFsL3Rlc3QvaW1wbC9zdHJlYW0uaHBw) | `99.31% <100.00%> (-0.01%)` | :arrow_down: |  
| [include/boost/beast/\_experimental/test/stream.hpp](https://app.codecov.io/gh/boostorg/beast/pull/3005?src=pr&el=tree&filepath=include%2Fboost%2Fbeast%2F_experimental%2Ftest%2Fstream.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9fZXhwZXJpbWVudGFsL3Rlc3Qvc3RyZWFtLmhwcA==) | `75.00% <ø> (ø)` | |  
| [include/boost/beast/core/async\_base.hpp](https://app.codecov.io/gh/boostorg/beast/pull/3005?src=pr&el=tree&filepath=include%2Fboost%2Fbeast%2Fcore%2Fasync_base.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9jb3JlL2FzeW5jX2Jhc2UuaHBw) | `100.00% <100.00%> (ø)` | |  
| [include/boost/beast/core/detail/async\_base.hpp](https://app.codecov.io/gh/boostorg/beast/pull/3005?src=pr&el=tree&filepath=include%2Fboost%2Fbeast%2Fcore%2Fdetail%2Fasync_base.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9jb3JlL2RldGFpbC9hc3luY19iYXNlLmhwcA==) | `100.00% <ø> (ø)` | |  
| [include/boost/beast/core/impl/saved\_handler.hpp](https://app.codecov.io/gh/boostorg/beast/pull/3005?src=pr&el=tree&filepath=include%2Fboost%2Fbeast%2Fcore%2Fimpl%2Fsaved_handler.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9jb3JlL2ltcGwvc2F2ZWRfaGFuZGxlci5ocHA=) | `100.00% <ø> (ø)` | |  
  
... and [25 files with indirect coverage changes](https://app.codecov.io/gh/boostorg/beast/pull/3005/indirect-changes?src=pr&el=tree-more&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/beast/pull/3005?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/beast/pull/3005?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [c5d20f5...ba22752](https://app.codecov.io/gh/boostorg/beast/pull/3005?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>  
<details><summary> :rocket: New features to boost your workflow: </summary>  
  
- :snowflake: [Test Analytics](https://docs.codecov.com/docs/test-analytics): Detect flaky tests, report on failures, and find test suite problems.  
</details>

---
