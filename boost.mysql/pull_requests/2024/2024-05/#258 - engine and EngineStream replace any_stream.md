# #258 engine and EngineStream replace any_stream [Merged]

> Username: anarthal  
> Created at: 2024-05-09 15:36:11 UTC  
> Updated at: 2024-05-10 12:03:06 UTC  
> Merged at: 2024-05-10 12:03:05 UTC  
> Closed at: 2024-05-10 12:03:05 UTC  
> Url: https://github.com/boostorg/mysql/pull/258  

`any_stream` has been replaced by the internal `EngineStream` concept and the `engine` interface.  
`algo_runner` has been replaced by `top_level_algo`, improving algorithm type-erasing  
  
This change is required by #195 and beneficial for #75

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2024-05-10 11:51:31 UTC  
> Url: https://github.com/boostorg/mysql/pull/258#issuecomment-2104474142  

## [Codecov](https://app.codecov.io/gh/boostorg/mysql/pull/258?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
Attention: Patch coverage is `96.98795%` with `10 lines` in your changes are missing coverage. Please review.  
> Project coverage is 98.51%. Comparing base [(`03f1dd5`)](https://app.codecov.io/gh/boostorg/mysql/commit/03f1dd5df212017564a8fd8c95bca69b0941fe6f?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) to head [(`613236b`)](https://app.codecov.io/gh/boostorg/mysql/pull/258?dropdown=coverage&src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/mysql/pull/258/graphs/tree.svg?width=650&height=150&src=pr&token=ajbbjm3OiO&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/mysql/pull/258?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #258      +/-   ##  
===========================================  
+ Coverage    98.35%   98.51%   +0.15%       
===========================================  
  Files          132      132                
  Lines         6200     6251      +51       
===========================================  
+ Hits          6098     6158      +60       
+ Misses         102       93       -9       
```  
  
| [Files](https://app.codecov.io/gh/boostorg/mysql/pull/258?dropdown=coverage&src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/mysql/any\_connection.hpp](https://app.codecov.io/gh/boostorg/mysql/pull/258?src=pr&el=tree&filepath=include%2Fboost%2Fmysql%2Fany_connection.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9teXNxbC9hbnlfY29ubmVjdGlvbi5ocHA=) | `98.81% <100.00%> (ø)` | |  
| [include/boost/mysql/connection.hpp](https://app.codecov.io/gh/boostorg/mysql/pull/258?src=pr&el=tree&filepath=include%2Fboost%2Fmysql%2Fconnection.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9teXNxbC9jb25uZWN0aW9uLmhwcA==) | `100.00% <100.00%> (ø)` | |  
| [include/boost/mysql/detail/any\_resumable\_ref.hpp](https://app.codecov.io/gh/boostorg/mysql/pull/258?src=pr&el=tree&filepath=include%2Fboost%2Fmysql%2Fdetail%2Fany_resumable_ref.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9teXNxbC9kZXRhaWwvYW55X3Jlc3VtYWJsZV9yZWYuaHBw) | `100.00% <100.00%> (ø)` | |  
| [include/boost/mysql/detail/connection\_impl.hpp](https://app.codecov.io/gh/boostorg/mysql/pull/258?src=pr&el=tree&filepath=include%2Fboost%2Fmysql%2Fdetail%2Fconnection_impl.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9teXNxbC9kZXRhaWwvY29ubmVjdGlvbl9pbXBsLmhwcA==) | `96.61% <100.00%> (+0.34%)` | :arrow_up: |  
| [include/boost/mysql/detail/engine.hpp](https://app.codecov.io/gh/boostorg/mysql/pull/258?src=pr&el=tree&filepath=include%2Fboost%2Fmysql%2Fdetail%2Fengine.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9teXNxbC9kZXRhaWwvZW5naW5lLmhwcA==) | `100.00% <100.00%> (ø)` | |  
| [include/boost/mysql/detail/next\_action.hpp](https://app.codecov.io/gh/boostorg/mysql/pull/258?src=pr&el=tree&filepath=include%2Fboost%2Fmysql%2Fdetail%2Fnext_action.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9teXNxbC9kZXRhaWwvbmV4dF9hY3Rpb24uaHBw) | `100.00% <ø> (ø)` | |  
| [include/boost/mysql/impl/any\_connection.ipp](https://app.codecov.io/gh/boostorg/mysql/pull/258?src=pr&el=tree&filepath=include%2Fboost%2Fmysql%2Fimpl%2Fany_connection.ipp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9teXNxbC9pbXBsL2FueV9jb25uZWN0aW9uLmlwcA==) | `100.00% <100.00%> (ø)` | |  
| [include/boost/mysql/impl/connection\_impl.ipp](https://app.codecov.io/gh/boostorg/mysql/pull/258?src=pr&el=tree&filepath=include%2Fboost%2Fmysql%2Fimpl%2Fconnection_impl.ipp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9teXNxbC9pbXBsL2Nvbm5lY3Rpb25faW1wbC5pcHA=) | `100.00% <100.00%> (ø)` | |  
| [.../internal/connection\_pool/connection\_pool\_impl.hpp](https://app.codecov.io/gh/boostorg/mysql/pull/258?src=pr&el=tree&filepath=include%2Fboost%2Fmysql%2Fimpl%2Finternal%2Fconnection_pool%2Fconnection_pool_impl.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9teXNxbC9pbXBsL2ludGVybmFsL2Nvbm5lY3Rpb25fcG9vbC9jb25uZWN0aW9uX3Bvb2xfaW1wbC5ocHA=) | `98.92% <100.00%> (+0.07%)` | :arrow_up: |  
| [...st/mysql/impl/internal/sansio/close\_connection.hpp](https://app.codecov.io/gh/boostorg/mysql/pull/258?src=pr&el=tree&filepath=include%2Fboost%2Fmysql%2Fimpl%2Finternal%2Fsansio%2Fclose_connection.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9teXNxbC9pbXBsL2ludGVybmFsL3NhbnNpby9jbG9zZV9jb25uZWN0aW9uLmhwcA==) | `92.85% <100.00%> (+2.85%)` | :arrow_up: |  
| ... and [19 more](https://app.codecov.io/gh/boostorg/mysql/pull/258?src=pr&el=tree-more&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | |  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/mysql/pull/258?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/mysql/pull/258?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [03f1dd5...613236b](https://app.codecov.io/gh/boostorg/mysql/pull/258?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---
