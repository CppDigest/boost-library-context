# #275 Pipeline API [Merged]

> Username: anarthal  
> Created at: 2024-06-03 16:21:04 UTC  
> Updated at: 2024-06-08 10:47:43 UTC  
> Merged at: 2024-06-08 10:47:42 UTC  
> Closed at: 2024-06-08 10:47:43 UTC  
> Url: https://github.com/boostorg/mysql/pull/275  

close #75

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2024-06-06 15:49:08 UTC  
> Updated_at: 2024-06-07 19:13:12 UTC  
> Url: https://github.com/boostorg/mysql/pull/275#issuecomment-2152862138  

## [Codecov](https://app.codecov.io/gh/boostorg/mysql/pull/275?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
Attention: Patch coverage is `98.62475%` with `7 lines` in your changes missing coverage. Please review.  
> Project coverage is 98.44%. Comparing base [(`22eddec`)](https://app.codecov.io/gh/boostorg/mysql/commit/22eddece250f8934f108d15bd8d977d3ed205270?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) to head [(`60ece2a`)](https://app.codecov.io/gh/boostorg/mysql/commit/60ece2a451f773bf1fb3016b6683dfbb80fc6b33?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/mysql/pull/275/graphs/tree.svg?width=650&height=150&src=pr&token=ajbbjm3OiO&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/mysql/pull/275?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #275      +/-   ##  
===========================================  
- Coverage    98.51%   98.44%   -0.07%       
===========================================  
  Files          132      136       +4       
  Lines         6250     6502     +252       
===========================================  
+ Hits          6157     6401     +244       
- Misses          93      101       +8       
```  
  
| [Files](https://app.codecov.io/gh/boostorg/mysql/pull/275?dropdown=coverage&src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/mysql/any\_connection.hpp](https://app.codecov.io/gh/boostorg/mysql/pull/275?src=pr&el=tree&filepath=include%2Fboost%2Fmysql%2Fany_connection.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9teXNxbC9hbnlfY29ubmVjdGlvbi5ocHA=) | `98.92% <100.00%> (+0.10%)` | :arrow_up: |  
| [include/boost/mysql/detail/connection\_impl.hpp](https://app.codecov.io/gh/boostorg/mysql/pull/275?src=pr&el=tree&filepath=include%2Fboost%2Fmysql%2Fdetail%2Fconnection_impl.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9teXNxbC9kZXRhaWwvY29ubmVjdGlvbl9pbXBsLmhwcA==) | `96.58% <100.00%> (-0.03%)` | :arrow_down: |  
| [include/boost/mysql/detail/engine\_impl.hpp](https://app.codecov.io/gh/boostorg/mysql/pull/275?src=pr&el=tree&filepath=include%2Fboost%2Fmysql%2Fdetail%2Fengine_impl.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9teXNxbC9kZXRhaWwvZW5naW5lX2ltcGwuaHBw) | `98.36% <100.00%> (ø)` | |  
| [include/boost/mysql/detail/next\_action.hpp](https://app.codecov.io/gh/boostorg/mysql/pull/275?src=pr&el=tree&filepath=include%2Fboost%2Fmysql%2Fdetail%2Fnext_action.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9teXNxbC9kZXRhaWwvbmV4dF9hY3Rpb24uaHBw) | `100.00% <100.00%> (ø)` | |  
| [include/boost/mysql/detail/throw\_on\_error\_loc.hpp](https://app.codecov.io/gh/boostorg/mysql/pull/275?src=pr&el=tree&filepath=include%2Fboost%2Fmysql%2Fdetail%2Fthrow_on_error_loc.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9teXNxbC9kZXRhaWwvdGhyb3dfb25fZXJyb3JfbG9jLmhwcA==) | `100.00% <100.00%> (ø)` | |  
| [...clude/boost/mysql/detail/writable\_field\_traits.hpp](https://app.codecov.io/gh/boostorg/mysql/pull/275?src=pr&el=tree&filepath=include%2Fboost%2Fmysql%2Fdetail%2Fwritable_field_traits.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9teXNxbC9kZXRhaWwvd3JpdGFibGVfZmllbGRfdHJhaXRzLmhwcA==) | `100.00% <ø> (ø)` | |  
| [include/boost/mysql/error\_with\_diagnostics.hpp](https://app.codecov.io/gh/boostorg/mysql/pull/275?src=pr&el=tree&filepath=include%2Fboost%2Fmysql%2Ferror_with_diagnostics.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9teXNxbC9lcnJvcl93aXRoX2RpYWdub3N0aWNzLmhwcA==) | `100.00% <100.00%> (ø)` | |  
| [include/boost/mysql/impl/connection\_impl.ipp](https://app.codecov.io/gh/boostorg/mysql/pull/275?src=pr&el=tree&filepath=include%2Fboost%2Fmysql%2Fimpl%2Fconnection_impl.ipp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9teXNxbC9pbXBsL2Nvbm5lY3Rpb25faW1wbC5pcHA=) | `100.00% <ø> (ø)` | |  
| [...nclude/boost/mysql/impl/error\_with\_diagnostics.ipp](https://app.codecov.io/gh/boostorg/mysql/pull/275?src=pr&el=tree&filepath=include%2Fboost%2Fmysql%2Fimpl%2Ferror_with_diagnostics.ipp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9teXNxbC9pbXBsL2Vycm9yX3dpdGhfZGlhZ25vc3RpY3MuaXBw) | `100.00% <100.00%> (ø)` | |  
| [.../impl/internal/connection\_pool/connection\_node.hpp](https://app.codecov.io/gh/boostorg/mysql/pull/275?src=pr&el=tree&filepath=include%2Fboost%2Fmysql%2Fimpl%2Finternal%2Fconnection_pool%2Fconnection_node.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9teXNxbC9pbXBsL2ludGVybmFsL2Nvbm5lY3Rpb25fcG9vbC9jb25uZWN0aW9uX25vZGUuaHBw) | `100.00% <100.00%> (ø)` | |  
| ... and [26 more](https://app.codecov.io/gh/boostorg/mysql/pull/275?src=pr&el=tree-more&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | |  
  
... and [1 file with indirect coverage changes](https://app.codecov.io/gh/boostorg/mysql/pull/275/indirect-changes?src=pr&el=tree-more&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/mysql/pull/275?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/mysql/pull/275?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [22eddec...60ece2a](https://app.codecov.io/gh/boostorg/mysql/pull/275?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---
