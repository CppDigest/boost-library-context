# #474 Handshake is now more resilient [Merged]

> Username: anarthal  
> Created at: 2025-04-27 20:32:27 UTC  
> Updated at: 2025-05-07 11:12:04 UTC  
> Merged at: 2025-05-07 11:11:52 UTC  
> Closed at: 2025-05-07 11:11:52 UTC  
> Url: https://github.com/boostorg/mysql/pull/474  

Errors issued by the server for caching_sha2_password users after a fast auth OK packet has been received (e.g. bad database errors) are now reported correctly  
Handshake now correctly detects protocol violation errors, like double auth switches  
  
close #469   
close #468

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2025-05-07 10:57:54 UTC  
> Updated_at: 2025-05-07 11:12:02 UTC  
> Url: https://github.com/boostorg/mysql/pull/474#issuecomment-2858118573  

## [Codecov](https://app.codecov.io/gh/boostorg/mysql/pull/474?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
Attention: Patch coverage is `99.39759%` with `1 line` in your changes missing coverage. Please review.  
> Project coverage is 99.15%. Comparing base [(`6839555`)](https://app.codecov.io/gh/boostorg/mysql/commit/6839555d836ee0a49b9577c9b0ae7e0bff039445?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) to head [(`40af68a`)](https://app.codecov.io/gh/boostorg/mysql/commit/40af68abef499395b955bf43c5cd202816dbe120?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> Report is 1 commits behind head on develop.  
  
| [Files with missing lines](https://app.codecov.io/gh/boostorg/mysql/pull/474?dropdown=coverage&src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Patch % | Lines |  
|---|---|---|  
| [...ude/boost/mysql/impl/internal/sansio/handshake.hpp](https://app.codecov.io/gh/boostorg/mysql/pull/474?src=pr&el=tree&filepath=include%2Fboost%2Fmysql%2Fimpl%2Finternal%2Fsansio%2Fhandshake.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9teXNxbC9pbXBsL2ludGVybmFsL3NhbnNpby9oYW5kc2hha2UuaHBw) | 98.87% | [1 Missing :warning: ](https://app.codecov.io/gh/boostorg/mysql/pull/474?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) |  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/mysql/pull/474/graphs/tree.svg?width=650&height=150&src=pr&token=ajbbjm3OiO&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/mysql/pull/474?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@           Coverage Diff            @@  
##           develop     #474   +/-   ##  
========================================  
  Coverage    99.14%   99.15%             
========================================  
  Files          142      143    +1       
  Lines         7278     7297   +19       
========================================  
+ Hits          7216     7235   +19       
  Misses          62       62             
```  
  
| [Files with missing lines](https://app.codecov.io/gh/boostorg/mysql/pull/474?dropdown=coverage&src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/mysql/client\_errc.hpp](https://app.codecov.io/gh/boostorg/mysql/pull/474?src=pr&el=tree&filepath=include%2Fboost%2Fmysql%2Fclient_errc.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9teXNxbC9jbGllbnRfZXJyYy5ocHA=) | `100.00% <ø> (ø)` | |  
| [include/boost/mysql/impl/error\_categories.ipp](https://app.codecov.io/gh/boostorg/mysql/pull/474?src=pr&el=tree&filepath=include%2Fboost%2Fmysql%2Fimpl%2Ferror_categories.ipp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9teXNxbC9pbXBsL2Vycm9yX2NhdGVnb3JpZXMuaXBw) | `98.78% <100.00%> (+0.03%)` | :arrow_up: |  
| [...t/mysql/impl/internal/protocol/deserialization.hpp](https://app.codecov.io/gh/boostorg/mysql/pull/474?src=pr&el=tree&filepath=include%2Fboost%2Fmysql%2Fimpl%2Finternal%2Fprotocol%2Fdeserialization.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9teXNxbC9pbXBsL2ludGVybmFsL3Byb3RvY29sL2Rlc2VyaWFsaXphdGlvbi5ocHA=) | `97.31% <100.00%> (-0.39%)` | :arrow_down: |  
| [...ost/mysql/impl/internal/protocol/static\_buffer.hpp](https://app.codecov.io/gh/boostorg/mysql/pull/474?src=pr&el=tree&filepath=include%2Fboost%2Fmysql%2Fimpl%2Finternal%2Fprotocol%2Fstatic_buffer.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9teXNxbC9pbXBsL2ludGVybmFsL3Byb3RvY29sL3N0YXRpY19idWZmZXIuaHBw) | `100.00% <100.00%> (ø)` | |  
| [...sql/impl/internal/sansio/caching\_sha2\_password.hpp](https://app.codecov.io/gh/boostorg/mysql/pull/474?src=pr&el=tree&filepath=include%2Fboost%2Fmysql%2Fimpl%2Finternal%2Fsansio%2Fcaching_sha2_password.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9teXNxbC9pbXBsL2ludGVybmFsL3NhbnNpby9jYWNoaW5nX3NoYTJfcGFzc3dvcmQuaHBw) | `100.00% <100.00%> (ø)` | |  
| [...sql/impl/internal/sansio/connection\_state\_data.hpp](https://app.codecov.io/gh/boostorg/mysql/pull/474?src=pr&el=tree&filepath=include%2Fboost%2Fmysql%2Fimpl%2Finternal%2Fsansio%2Fconnection_state_data.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9teXNxbC9pbXBsL2ludGVybmFsL3NhbnNpby9jb25uZWN0aW9uX3N0YXRlX2RhdGEuaHBw) | `100.00% <100.00%> (ø)` | |  
| [...sql/impl/internal/sansio/mysql\_native\_password.hpp](https://app.codecov.io/gh/boostorg/mysql/pull/474?src=pr&el=tree&filepath=include%2Fboost%2Fmysql%2Fimpl%2Finternal%2Fsansio%2Fmysql_native_password.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9teXNxbC9pbXBsL2ludGVybmFsL3NhbnNpby9teXNxbF9uYXRpdmVfcGFzc3dvcmQuaHBw) | `100.00% <100.00%> (ø)` | |  
| [include/boost/mysql/impl/is\_fatal\_error.ipp](https://app.codecov.io/gh/boostorg/mysql/pull/474?src=pr&el=tree&filepath=include%2Fboost%2Fmysql%2Fimpl%2Fis_fatal_error.ipp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9teXNxbC9pbXBsL2lzX2ZhdGFsX2Vycm9yLmlwcA==) | `100.00% <100.00%> (ø)` | |  
| [...ude/boost/mysql/impl/internal/sansio/handshake.hpp](https://app.codecov.io/gh/boostorg/mysql/pull/474?src=pr&el=tree&filepath=include%2Fboost%2Fmysql%2Fimpl%2Finternal%2Fsansio%2Fhandshake.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9teXNxbC9pbXBsL2ludGVybmFsL3NhbnNpby9oYW5kc2hha2UuaHBw) | `99.23% <98.87%> (+0.95%)` | :arrow_up: |  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/mysql/pull/474?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/mysql/pull/474?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [6839555...40af68a](https://app.codecov.io/gh/boostorg/mysql/pull/474?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>  
<details><summary> :rocket: New features to boost your workflow: </summary>  
  
- :snowflake: [Test Analytics](https://docs.codecov.com/docs/test-analytics): Detect flaky tests, report on failures, and find test suite problems.  
</details>

---
