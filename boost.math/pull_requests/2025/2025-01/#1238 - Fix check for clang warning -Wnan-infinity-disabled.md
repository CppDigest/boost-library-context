# #1238 Fix check for clang warning -Wnan-infinity-disabled [Merged]

> Username: pps83  
> Created at: 2025-01-23 15:56:14 UTC  
> Updated at: 2025-01-23 19:04:12 UTC  
> Merged at: 2025-01-23 19:01:27 UTC  
> Closed at: 2025-01-23 19:01:27 UTC  
> Url: https://github.com/boostorg/math/pull/1238  

with latest changes I still had warnings with icx2024 (intel's new compiler version 2024).

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2025-01-23 17:02:27 UTC  
> Updated_at: 2025-01-23 19:02:00 UTC  
> Url: https://github.com/boostorg/math/pull/1238#issuecomment-2610431359  

## [Codecov](https://app.codecov.io/gh/boostorg/math/pull/1238?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
All modified and coverable lines are covered by tests :white_check_mark:  
> Project coverage is 93.83%. Comparing base [(`2f3c798`)](https://app.codecov.io/gh/boostorg/math/commit/2f3c7985f797734a5df42d846cd77ec6101270ef?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) to head [(`dfa0bd6`)](https://app.codecov.io/gh/boostorg/math/commit/dfa0bd6ee676354c0e0b6fd3d70fd465a8b451ce?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> Report is 4 commits behind head on develop.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/math/pull/1238/graphs/tree.svg?width=650&height=150&src=pr&token=26IDb1OAa7&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/math/pull/1238?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@           Coverage Diff            @@  
##           develop    #1238   +/-   ##  
========================================  
  Coverage    93.83%   93.83%             
========================================  
  Files          657      657             
  Lines        55244    55244             
========================================  
  Hits         51840    51840             
  Misses        3404     3404             
```  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/math/pull/1238?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/math/pull/1238?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [2f3c798...dfa0bd6](https://app.codecov.io/gh/boostorg/math/pull/1238?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---

## Review 2 [Approved]

> Username: mborland  
> Created_at: 2025-01-23 19:01:19 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/math/pull/1238#pullrequestreview-2570782501  

LGTM

---
