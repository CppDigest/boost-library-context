# #299 Added with_params [Merged]

> Username: anarthal  
> Created at: 2024-07-02 11:48:52 UTC  
> Updated at: 2024-08-01 10:59:58 UTC  
> Merged at: 2024-08-01 10:59:56 UTC  
> Closed at: 2024-08-01 10:59:56 UTC  
> Url: https://github.com/boostorg/mysql/pull/299  

close #218

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2024-07-02 12:44:59 UTC  
> Updated_at: 2024-07-31 22:41:00 UTC  
> Url: https://github.com/boostorg/mysql/pull/299#issuecomment-2203068409  

## [Codecov](https://app.codecov.io/gh/boostorg/mysql/pull/299?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
Attention: Patch coverage is `98.18182%` with `2 lines` in your changes missing coverage. Please review.  
> Project coverage is 98.60%. Comparing base [(`f76e17c`)](https://app.codecov.io/gh/boostorg/mysql/commit/f76e17c37bac6e60458d491ac5b676407493892e?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) to head [(`4983929`)](https://app.codecov.io/gh/boostorg/mysql/commit/49839296193e5b64e0fcdf4ba1fcb1db7ab5d9f8?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
| [Files](https://app.codecov.io/gh/boostorg/mysql/pull/299?dropdown=coverage&src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Patch % | Lines |  
|---|---|---|  
| [...ost/mysql/impl/internal/sansio/start\_execution.hpp](https://app.codecov.io/gh/boostorg/mysql/pull/299?src=pr&el=tree&filepath=include%2Fboost%2Fmysql%2Fimpl%2Finternal%2Fsansio%2Fstart_execution.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9teXNxbC9pbXBsL2ludGVybmFsL3NhbnNpby9zdGFydF9leGVjdXRpb24uaHBw) | 93.10% | [2 Missing :warning: ](https://app.codecov.io/gh/boostorg/mysql/pull/299?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) |  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/mysql/pull/299/graphs/tree.svg?width=650&height=150&src=pr&token=ajbbjm3OiO&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/mysql/pull/299?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #299      +/-   ##  
===========================================  
- Coverage    98.68%   98.60%   -0.08%       
===========================================  
  Files          138      140       +2       
  Lines         7069     7108      +39       
===========================================  
+ Hits          6976     7009      +33       
- Misses          93       99       +6       
```  
  
| [Files](https://app.codecov.io/gh/boostorg/mysql/pull/299?dropdown=coverage&src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/mysql/any\_connection.hpp](https://app.codecov.io/gh/boostorg/mysql/pull/299?src=pr&el=tree&filepath=include%2Fboost%2Fmysql%2Fany_connection.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9teXNxbC9hbnlfY29ubmVjdGlvbi5ocHA=) | `100.00% <100.00%> (ø)` | |  
| [include/boost/mysql/connection.hpp](https://app.codecov.io/gh/boostorg/mysql/pull/299?src=pr&el=tree&filepath=include%2Fboost%2Fmysql%2Fconnection.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9teXNxbC9jb25uZWN0aW9uLmhwcA==) | `98.36% <100.00%> (-1.64%)` | :arrow_down: |  
| [...clude/boost/mysql/detail/any\_execution\_request.hpp](https://app.codecov.io/gh/boostorg/mysql/pull/299?src=pr&el=tree&filepath=include%2Fboost%2Fmysql%2Fdetail%2Fany_execution_request.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9teXNxbC9kZXRhaWwvYW55X2V4ZWN1dGlvbl9yZXF1ZXN0LmhwcA==) | `100.00% <100.00%> (ø)` | |  
| [include/boost/mysql/detail/connection\_impl.hpp](https://app.codecov.io/gh/boostorg/mysql/pull/299?src=pr&el=tree&filepath=include%2Fboost%2Fmysql%2Fdetail%2Fconnection_impl.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9teXNxbC9kZXRhaWwvY29ubmVjdGlvbl9pbXBsLmhwcA==) | `90.40% <100.00%> (-1.52%)` | :arrow_down: |  
| [include/boost/mysql/detail/format\_sql.hpp](https://app.codecov.io/gh/boostorg/mysql/pull/299?src=pr&el=tree&filepath=include%2Fboost%2Fmysql%2Fdetail%2Fformat_sql.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9teXNxbC9kZXRhaWwvZm9ybWF0X3NxbC5ocHA=) | `100.00% <ø> (ø)` | |  
| [include/boost/mysql/format\_sql.hpp](https://app.codecov.io/gh/boostorg/mysql/pull/299?src=pr&el=tree&filepath=include%2Fboost%2Fmysql%2Fformat_sql.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9teXNxbC9mb3JtYXRfc3FsLmhwcA==) | `100.00% <100.00%> (ø)` | |  
| [include/boost/mysql/impl/format\_sql.hpp](https://app.codecov.io/gh/boostorg/mysql/pull/299?src=pr&el=tree&filepath=include%2Fboost%2Fmysql%2Fimpl%2Fformat_sql.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9teXNxbC9pbXBsL2Zvcm1hdF9zcWwuaHBw) | `100.00% <ø> (ø)` | |  
| [include/boost/mysql/impl/format\_sql.ipp](https://app.codecov.io/gh/boostorg/mysql/pull/299?src=pr&el=tree&filepath=include%2Fboost%2Fmysql%2Fimpl%2Fformat_sql.ipp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9teXNxbC9pbXBsL2Zvcm1hdF9zcWwuaXBw) | `99.16% <100.00%> (ø)` | |  
| [...l/internal/protocol/impl/serialization\_context.hpp](https://app.codecov.io/gh/boostorg/mysql/pull/299?src=pr&el=tree&filepath=include%2Fboost%2Fmysql%2Fimpl%2Finternal%2Fprotocol%2Fimpl%2Fserialization_context.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9teXNxbC9pbXBsL2ludGVybmFsL3Byb3RvY29sL2ltcGwvc2VyaWFsaXphdGlvbl9jb250ZXh0LmhwcA==) | `100.00% <100.00%> (ø)` | |  
| [include/boost/mysql/impl/statement.hpp](https://app.codecov.io/gh/boostorg/mysql/pull/299?src=pr&el=tree&filepath=include%2Fboost%2Fmysql%2Fimpl%2Fstatement.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9teXNxbC9pbXBsL3N0YXRlbWVudC5ocHA=) | `100.00% <100.00%> (ø)` | |  
| ... and [3 more](https://app.codecov.io/gh/boostorg/mysql/pull/299?src=pr&el=tree-more&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | |  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/mysql/pull/299?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/mysql/pull/299?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [f76e17c...4983929](https://app.codecov.io/gh/boostorg/mysql/pull/299?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---
