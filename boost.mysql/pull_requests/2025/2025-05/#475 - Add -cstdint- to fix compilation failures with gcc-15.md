# #475 Add <cstdint> to fix compilation failures with gcc-15. [Merged]

> Username: hhoffstaette  
> Created at: 2025-05-06 21:13:52 UTC  
> Updated at: 2025-05-07 09:55:52 UTC  
> Merged at: 2025-05-07 09:55:15 UTC  
> Closed at: 2025-05-07 09:55:15 UTC  
> Url: https://github.com/boostorg/mysql/pull/475  

This fixes compilation failures we've seen in Gentoo when trying to run the boost testuite.

---

## Comment 1

> Username: anarthal  
> Created_at: 2025-05-07 09:23:06 UTC  
> Url: https://github.com/boostorg/mysql/pull/475#issuecomment-2857827217  

Looks like a problem derived from using precompiled headers in most of the unit tests. Thanks for the PR, will merge aftr CI passes.

---

## Comment 2

> Username: codecov[bot]  
> Created_at: 2025-05-07 09:46:33 UTC  
> Updated_at: 2025-05-07 09:55:51 UTC  
> Url: https://github.com/boostorg/mysql/pull/475#issuecomment-2857907339  

## [Codecov](https://app.codecov.io/gh/boostorg/mysql/pull/475?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
All modified and coverable lines are covered by tests :white_check_mark:  
> Project coverage is 99.14%. Comparing base [(`27e417d`)](https://app.codecov.io/gh/boostorg/mysql/commit/27e417dc2de9c75893f45f28c58c1062e992ede8?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) to head [(`9fc0379`)](https://app.codecov.io/gh/boostorg/mysql/commit/9fc037958bd107dc87e7b4404db30785e0b3b139?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> Report is 1 commits behind head on develop.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/mysql/pull/475/graphs/tree.svg?width=650&height=150&src=pr&token=ajbbjm3OiO&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/mysql/pull/475?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@           Coverage Diff            @@  
##           develop     #475   +/-   ##  
========================================  
  Coverage    99.14%   99.14%             
========================================  
  Files          142      142             
  Lines         7278     7278             
========================================  
  Hits          7216     7216             
  Misses          62       62             
```  
  
| [Files with missing lines](https://app.codecov.io/gh/boostorg/mysql/pull/475?dropdown=coverage&src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [...ost/mysql/impl/internal/protocol/static\_buffer.hpp](https://app.codecov.io/gh/boostorg/mysql/pull/475?src=pr&el=tree&filepath=include%2Fboost%2Fmysql%2Fimpl%2Finternal%2Fprotocol%2Fstatic_buffer.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9teXNxbC9pbXBsL2ludGVybmFsL3Byb3RvY29sL3N0YXRpY19idWZmZXIuaHBw) | `100.00% <ø> (ø)` | |  
| [include/boost/mysql/metadata.hpp](https://app.codecov.io/gh/boostorg/mysql/pull/475?src=pr&el=tree&filepath=include%2Fboost%2Fmysql%2Fmetadata.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9teXNxbC9tZXRhZGF0YS5ocHA=) | `100.00% <ø> (ø)` | |  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/mysql/pull/475?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/mysql/pull/475?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [27e417d...9fc0379](https://app.codecov.io/gh/boostorg/mysql/pull/475?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>  
<details><summary> :rocket: New features to boost your workflow: </summary>  
  
- :snowflake: [Test Analytics](https://docs.codecov.com/docs/test-analytics): Detect flaky tests, report on failures, and find test suite problems.  
</details>

---
