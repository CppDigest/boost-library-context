# #3036 Fix allocator move/copy assignment in flat_buffer and multi_buffer [Merged]

> Username: ashtum  
> Created at: 2025-10-02 12:55:44 UTC  
> Updated at: 2026-01-18 06:59:18 UTC  
> Merged at: 2025-10-03 18:41:12 UTC  
> Closed at: 2025-10-03 18:41:12 UTC  
> Url: https://github.com/boostorg/beast/pull/3036  

Fixes #3034

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2025-10-03 13:21:48 UTC  
> Updated_at: 2025-10-03 19:11:07 UTC  
> Url: https://github.com/boostorg/beast/pull/3036#issuecomment-3365667707  

## [Codecov](https://app.codecov.io/gh/boostorg/beast/pull/3036?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
:white_check_mark: All modified and coverable lines are covered by tests.  
:white_check_mark: Project coverage is 93.18%. Comparing base ([`164db4b`](https://app.codecov.io/gh/boostorg/beast/commit/164db4bc57707b02550a53902cb1c138da99789f?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)) to head ([`dfc7ce8`](https://app.codecov.io/gh/boostorg/beast/commit/dfc7ce8f1611dce53ec7dea383549ab31db56aba?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)).  
:warning: Report is 2 commits behind head on develop.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/beast/pull/3036/graphs/tree.svg?width=650&height=150&src=pr&token=Gq6MFA9JRF&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/beast/pull/3036?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@             Coverage Diff             @@  
##           develop    #3036      +/-   ##  
===========================================  
+ Coverage    93.16%   93.18%   +0.01%       
===========================================  
  Files          176      176                
  Lines        13656    13684      +28       
===========================================  
+ Hits         12723    12751      +28       
  Misses         933      933                
```  
  
| [Files with missing lines](https://app.codecov.io/gh/boostorg/beast/pull/3036?dropdown=coverage&src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/beast/core/impl/flat\_buffer.hpp](https://app.codecov.io/gh/boostorg/beast/pull/3036?src=pr&el=tree&filepath=include%2Fboost%2Fbeast%2Fcore%2Fimpl%2Fflat_buffer.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9jb3JlL2ltcGwvZmxhdF9idWZmZXIuaHBw) | `98.61% <100.00%> (+0.05%)` | :arrow_up: |  
| [include/boost/beast/core/impl/multi\_buffer.hpp](https://app.codecov.io/gh/boostorg/beast/pull/3036?src=pr&el=tree&filepath=include%2Fboost%2Fbeast%2Fcore%2Fimpl%2Fmulti_buffer.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9jb3JlL2ltcGwvbXVsdGlfYnVmZmVyLmhwcA==) | `97.70% <100.00%> (+0.07%)` | :arrow_up: |  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/beast/pull/3036?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/beast/pull/3036?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [164db4b...dfc7ce8](https://app.codecov.io/gh/boostorg/beast/pull/3036?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
</details>  
<details><summary> :rocket: New features to boost your workflow: </summary>  
  
- :snowflake: [Test Analytics](https://docs.codecov.com/docs/test-analytics): Detect flaky tests, report on failures, and find test suite problems.  
</details>

---
