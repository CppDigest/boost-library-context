# #300 Disabled Naggle's algorithm in any_connection [Merged]

> Username: anarthal  
> Created at: 2024-07-02 15:39:02 UTC  
> Updated at: 2024-07-02 16:35:05 UTC  
> Merged at: 2024-07-02 16:35:03 UTC  
> Closed at: 2024-07-02 16:35:03 UTC  
> Url: https://github.com/boostorg/mysql/pull/300  

Fixes a performance issue with pipelines only containing close statement stages.  
  
close #277

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2024-07-02 16:31:43 UTC  
> Url: https://github.com/boostorg/mysql/pull/300#issuecomment-2203781768  

## [Codecov](https://app.codecov.io/gh/boostorg/mysql/pull/300?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
Attention: Patch coverage is `85.71429%` with `1 line` in your changes missing coverage. Please review.  
> Project coverage is 98.52%. Comparing base [(`0944b2a`)](https://app.codecov.io/gh/boostorg/mysql/commit/0944b2af77502f0ff9d85dc890602299694fda13?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) to head [(`6c8f962`)](https://app.codecov.io/gh/boostorg/mysql/commit/6c8f9622ed34a3f9ea9412328d5cfc92b91d914f?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/mysql/pull/300/graphs/tree.svg?width=650&height=150&src=pr&token=ajbbjm3OiO&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/mysql/pull/300?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #300      +/-   ##  
===========================================  
- Coverage    98.53%   98.52%   -0.02%       
===========================================  
  Files          134      134                
  Lines         6480     6487       +7       
===========================================  
+ Hits          6385     6391       +6       
- Misses          95       96       +1       
```  
  
| [Files](https://app.codecov.io/gh/boostorg/mysql/pull/300?dropdown=coverage&src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [...clude/boost/mysql/impl/internal/variant\_stream.hpp](https://app.codecov.io/gh/boostorg/mysql/pull/300?src=pr&el=tree&filepath=include%2Fboost%2Fmysql%2Fimpl%2Finternal%2Fvariant_stream.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9teXNxbC9pbXBsL2ludGVybmFsL3ZhcmlhbnRfc3RyZWFtLmhwcA==) | `90.00% <85.71%> (-0.33%)` | :arrow_down: |  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/mysql/pull/300?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/mysql/pull/300?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [0944b2a...6c8f962](https://app.codecov.io/gh/boostorg/mysql/pull/300?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---
