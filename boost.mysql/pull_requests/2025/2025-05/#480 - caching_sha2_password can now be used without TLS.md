# #480 caching_sha2_password can now be used without TLS [Merged]

> Username: anarthal  
> Created at: 2025-05-11 14:40:14 UTC  
> Updated at: 2025-06-07 11:20:57 UTC  
> Merged at: 2025-06-07 11:20:48 UTC  
> Closed at: 2025-06-07 11:20:48 UTC  
> Url: https://github.com/boostorg/mysql/pull/480  

close #313

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2025-06-05 10:46:12 UTC  
> Updated_at: 2025-06-07 11:20:56 UTC  
> Url: https://github.com/boostorg/mysql/pull/480#issuecomment-2943691156  

## [Codecov](https://app.codecov.io/gh/boostorg/mysql/pull/480?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
All modified and coverable lines are covered by tests :white_check_mark:  
> Project coverage is 99.15%. Comparing base [(`90405e7`)](https://app.codecov.io/gh/boostorg/mysql/commit/90405e79e4590f142e28094eea1625f259ffeec4?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) to head [(`dfb962a`)](https://app.codecov.io/gh/boostorg/mysql/commit/dfb962a0b26cdfcb27909df1a01537a1cac5f6aa?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> Report is 1 commits behind head on develop.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/mysql/pull/480/graphs/tree.svg?width=650&height=150&src=pr&token=ajbbjm3OiO&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/mysql/pull/480?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@           Coverage Diff            @@  
##           develop     #480   +/-   ##  
========================================  
  Coverage    99.15%   99.15%             
========================================  
  Files          143      144    +1       
  Lines         7297     7370   +73       
========================================  
+ Hits          7235     7308   +73       
  Misses          62       62             
```  
  
| [Files with missing lines](https://app.codecov.io/gh/boostorg/mysql/pull/480?dropdown=coverage&src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/mysql/client\_errc.hpp](https://app.codecov.io/gh/boostorg/mysql/pull/480?src=pr&el=tree&filepath=include%2Fboost%2Fmysql%2Fclient_errc.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9teXNxbC9jbGllbnRfZXJyYy5ocHA=) | `100.00% <ø> (ø)` | |  
| [include/boost/mysql/impl/error\_categories.ipp](https://app.codecov.io/gh/boostorg/mysql/pull/480?src=pr&el=tree&filepath=include%2Fboost%2Fmysql%2Fimpl%2Ferror_categories.ipp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9teXNxbC9pbXBsL2Vycm9yX2NhdGVnb3JpZXMuaXBw) | `98.80% <100.00%> (+0.02%)` | :arrow_up: |  
| [...sql/impl/internal/sansio/caching\_sha2\_password.hpp](https://app.codecov.io/gh/boostorg/mysql/pull/480?src=pr&el=tree&filepath=include%2Fboost%2Fmysql%2Fimpl%2Finternal%2Fsansio%2Fcaching_sha2_password.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9teXNxbC9pbXBsL2ludGVybmFsL3NhbnNpby9jYWNoaW5nX3NoYTJfcGFzc3dvcmQuaHBw) | `100.00% <100.00%> (ø)` | |  
| [...l/impl/internal/sansio/csha2p\_encrypt\_password.hpp](https://app.codecov.io/gh/boostorg/mysql/pull/480?src=pr&el=tree&filepath=include%2Fboost%2Fmysql%2Fimpl%2Finternal%2Fsansio%2Fcsha2p_encrypt_password.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9teXNxbC9pbXBsL2ludGVybmFsL3NhbnNpby9jc2hhMnBfZW5jcnlwdF9wYXNzd29yZC5ocHA=) | `100.00% <100.00%> (ø)` | |  
| [...ude/boost/mysql/impl/internal/sansio/handshake.hpp](https://app.codecov.io/gh/boostorg/mysql/pull/480?src=pr&el=tree&filepath=include%2Fboost%2Fmysql%2Fimpl%2Finternal%2Fsansio%2Fhandshake.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9teXNxbC9pbXBsL2ludGVybmFsL3NhbnNpby9oYW5kc2hha2UuaHBw) | `99.31% <100.00%> (+0.08%)` | :arrow_up: |  
| [...sql/impl/internal/sansio/mysql\_native\_password.hpp](https://app.codecov.io/gh/boostorg/mysql/pull/480?src=pr&el=tree&filepath=include%2Fboost%2Fmysql%2Fimpl%2Finternal%2Fsansio%2Fmysql_native_password.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9teXNxbC9pbXBsL2ludGVybmFsL3NhbnNpby9teXNxbF9uYXRpdmVfcGFzc3dvcmQuaHBw) | `100.00% <100.00%> (ø)` | |  
| [include/boost/mysql/impl/is\_fatal\_error.ipp](https://app.codecov.io/gh/boostorg/mysql/pull/480?src=pr&el=tree&filepath=include%2Fboost%2Fmysql%2Fimpl%2Fis_fatal_error.ipp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9teXNxbC9pbXBsL2lzX2ZhdGFsX2Vycm9yLmlwcA==) | `100.00% <100.00%> (ø)` | |  
| [include/boost/mysql/impl/pfr.hpp](https://app.codecov.io/gh/boostorg/mysql/pull/480?src=pr&el=tree&filepath=include%2Fboost%2Fmysql%2Fimpl%2Fpfr.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9teXNxbC9pbXBsL3Bmci5ocHA=) | `100.00% <ø> (ø)` | |  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/mysql/pull/480?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/mysql/pull/480?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [90405e7...dfb962a](https://app.codecov.io/gh/boostorg/mysql/pull/480?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>  
<details><summary> :rocket: New features to boost your workflow: </summary>  
  
- :snowflake: [Test Analytics](https://docs.codecov.com/docs/test-analytics): Detect flaky tests, report on failures, and find test suite problems.  
</details>

---
