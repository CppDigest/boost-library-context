# #257 Pipeline-friendly serialization [Merged]

> Username: anarthal  
> Created at: 2024-05-05 17:17:23 UTC  
> Updated at: 2024-05-08 09:02:19 UTC  
> Merged at: 2024-05-08 09:02:19 UTC  
> Closed at: 2024-05-08 09:02:19 UTC  
> Url: https://github.com/boostorg/mysql/pull/257  

Updated the serialization functions to take framing into account when serializing. This is a requirement to implement #75.  
  
close #255

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2024-05-06 19:32:54 UTC  
> Updated_at: 2024-05-07 01:13:51 UTC  
> Url: https://github.com/boostorg/mysql/pull/257#issuecomment-2096761471  

## [Codecov](https://app.codecov.io/gh/boostorg/mysql/pull/257?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
Attention: Patch coverage is `99.12127%` with `5 lines` in your changes are missing coverage. Please review.  
> Project coverage is 98.35%. Comparing base [(`75c7415`)](https://app.codecov.io/gh/boostorg/mysql/commit/75c74151181c8d9683f318a32680ffa201c3590e?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) to head [(`0516ba9`)](https://app.codecov.io/gh/boostorg/mysql/pull/257?dropdown=coverage&src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/mysql/pull/257/graphs/tree.svg?width=650&height=150&src=pr&token=ajbbjm3OiO&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/mysql/pull/257?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #257      +/-   ##  
===========================================  
- Coverage    98.37%   98.35%   -0.03%       
===========================================  
  Files          130      132       +2       
  Lines         6344     6200     -144       
===========================================  
- Hits          6241     6098     -143       
+ Misses         103      102       -1       
```  
  
| [Files](https://app.codecov.io/gh/boostorg/mysql/pull/257?dropdown=coverage&src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/mysql/impl/error\_categories.ipp](https://app.codecov.io/gh/boostorg/mysql/pull/257?src=pr&el=tree&filepath=include%2Fboost%2Fmysql%2Fimpl%2Ferror_categories.ipp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9teXNxbC9pbXBsL2Vycm9yX2NhdGVnb3JpZXMuaXBw) | `90.90% <100.00%> (ø)` | |  
| [include/boost/mysql/impl/execution\_state\_impl.ipp](https://app.codecov.io/gh/boostorg/mysql/pull/257?src=pr&el=tree&filepath=include%2Fboost%2Fmysql%2Fimpl%2Fexecution_state_impl.ipp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9teXNxbC9pbXBsL2V4ZWN1dGlvbl9zdGF0ZV9pbXBsLmlwcA==) | `100.00% <ø> (ø)` | |  
| [include/boost/mysql/impl/internal/auth/auth.ipp](https://app.codecov.io/gh/boostorg/mysql/pull/257?src=pr&el=tree&filepath=include%2Fboost%2Fmysql%2Fimpl%2Finternal%2Fauth%2Fauth.ipp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9teXNxbC9pbXBsL2ludGVybmFsL2F1dGgvYXV0aC5pcHA=) | `100.00% <100.00%> (ø)` | |  
| [...sql/impl/internal/error/server\_error\_to\_string.ipp](https://app.codecov.io/gh/boostorg/mysql/pull/257?src=pr&el=tree&filepath=include%2Fboost%2Fmysql%2Fimpl%2Finternal%2Ferror%2Fserver_error_to_string.ipp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9teXNxbC9pbXBsL2ludGVybmFsL2Vycm9yL3NlcnZlcl9lcnJvcl90b19zdHJpbmcuaXBw) | `100.00% <100.00%> (ø)` | |  
| [...t/mysql/impl/internal/protocol/deserialization.hpp](https://app.codecov.io/gh/boostorg/mysql/pull/257?src=pr&el=tree&filepath=include%2Fboost%2Fmysql%2Fimpl%2Finternal%2Fprotocol%2Fdeserialization.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9teXNxbC9pbXBsL2ludGVybmFsL3Byb3RvY29sL2Rlc2VyaWFsaXphdGlvbi5ocHA=) | `97.23% <100.00%> (ø)` | |  
| [...oost/mysql/impl/internal/protocol/frame\_header.hpp](https://app.codecov.io/gh/boostorg/mysql/pull/257?src=pr&el=tree&filepath=include%2Fboost%2Fmysql%2Fimpl%2Finternal%2Fprotocol%2Fframe_header.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9teXNxbC9pbXBsL2ludGVybmFsL3Byb3RvY29sL2ZyYW1lX2hlYWRlci5ocHA=) | `100.00% <100.00%> (ø)` | |  
| [.../mysql/impl/internal/protocol/impl/null\_bitmap.hpp](https://app.codecov.io/gh/boostorg/mysql/pull/257?src=pr&el=tree&filepath=include%2Fboost%2Fmysql%2Fimpl%2Finternal%2Fprotocol%2Fimpl%2Fnull_bitmap.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9teXNxbC9pbXBsL2ludGVybmFsL3Byb3RvY29sL2ltcGwvbnVsbF9iaXRtYXAuaHBw) | `100.00% <100.00%> (ø)` | |  
| [...mpl/internal/protocol/impl/protocol\_field\_type.hpp](https://app.codecov.io/gh/boostorg/mysql/pull/257?src=pr&el=tree&filepath=include%2Fboost%2Fmysql%2Fimpl%2Finternal%2Fprotocol%2Fimpl%2Fprotocol_field_type.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9teXNxbC9pbXBsL2ludGVybmFsL3Byb3RvY29sL2ltcGwvcHJvdG9jb2xfZmllbGRfdHlwZS5ocHA=) | `92.30% <100.00%> (ø)` | |  
| [...l/internal/protocol/impl/serialization\_context.hpp](https://app.codecov.io/gh/boostorg/mysql/pull/257?src=pr&el=tree&filepath=include%2Fboost%2Fmysql%2Fimpl%2Finternal%2Fprotocol%2Fimpl%2Fserialization_context.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9teXNxbC9pbXBsL2ludGVybmFsL3Byb3RvY29sL2ltcGwvc2VyaWFsaXphdGlvbl9jb250ZXh0LmhwcA==) | `100.00% <100.00%> (ø)` | |  
| [.../mysql/impl/internal/protocol/impl/span\_string.hpp](https://app.codecov.io/gh/boostorg/mysql/pull/257?src=pr&el=tree&filepath=include%2Fboost%2Fmysql%2Fimpl%2Finternal%2Fprotocol%2Fimpl%2Fspan_string.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9teXNxbC9pbXBsL2ludGVybmFsL3Byb3RvY29sL2ltcGwvc3Bhbl9zdHJpbmcuaHBw) | `100.00% <100.00%> (ø)` | |  
| ... and [19 more](https://app.codecov.io/gh/boostorg/mysql/pull/257?src=pr&el=tree-more&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | |  
  
... and [1 file with indirect coverage changes](https://app.codecov.io/gh/boostorg/mysql/pull/257/indirect-changes?src=pr&el=tree-more&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/mysql/pull/257?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/mysql/pull/257?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [75c7415...0516ba9](https://app.codecov.io/gh/boostorg/mysql/pull/257?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---
