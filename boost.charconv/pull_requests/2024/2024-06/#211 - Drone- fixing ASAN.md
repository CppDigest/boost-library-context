# #211 Drone: fixing ASAN [Merged]

> Username: sdarwin  
> Created at: 2024-06-25 17:17:21 UTC  
> Updated at: 2024-06-25 18:09:47 UTC  
> Merged at: 2024-06-25 18:09:47 UTC  
> Closed at: 2024-06-25 18:09:47 UTC  
> Url: https://github.com/boostorg/charconv/pull/211  

ASAN often requires privileged access.   
  
This PR is overly simplified, setting `privileged: true` on all linux jobs.  You should modify the function and set `privileged: true` when necessary.    
  
The `randomize_va_space` setting was discovered on stackoverflow, and may help.  It can also be isolated to apply to ASAN jobs instead of everything.    
  
Any repos that need this specific update should contact me and request the Drone setting "Trusted".   I have also modified cppalliance/decimal.

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2024-06-25 17:51:40 UTC  
> Url: https://github.com/boostorg/charconv/pull/211#issuecomment-2189611577  

## [Codecov](https://app.codecov.io/gh/boostorg/charconv/pull/211?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
All modified and coverable lines are covered by tests :white_check_mark:  
> Project coverage is 94.52%. Comparing base [(`9db7af7`)](https://app.codecov.io/gh/boostorg/charconv/commit/9db7af735e435132d4849ad02a08eb7579103601?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) to head [(`ff2b1e3`)](https://app.codecov.io/gh/boostorg/charconv/commit/ff2b1e398fa06a6aa8a2d0356246a7b98988499b?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/charconv/pull/211/graphs/tree.svg?width=650&height=150&src=pr&token=jTnxtbJoTv&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/charconv/pull/211?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@           Coverage Diff            @@  
##           develop     #211   +/-   ##  
========================================  
  Coverage    94.52%   94.52%             
========================================  
  Files           66       66             
  Lines         8459     8459             
========================================  
  Hits          7996     7996             
  Misses         463      463             
```  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/charconv/pull/211?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/charconv/pull/211?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [9db7af7...ff2b1e3](https://app.codecov.io/gh/boostorg/charconv/pull/211?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---
