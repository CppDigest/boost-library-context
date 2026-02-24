# #191 Make `getenv` thread-safe [Merged]

> Username: Flamefire  
> Created at: 2024-11-29 09:14:13 UTC  
> Updated at: 2024-12-20 13:36:21 UTC  
> Merged at: 2024-12-20 13:36:13 UTC  
> Closed at: 2024-12-20 13:36:13 UTC  
> Url: https://github.com/boostorg/nowide/pull/191  

In order to return a non-owning pointer without memory leaks the function needs to use a static variable.  
When calling it from multiple threads there is a data race during the assignment (and conversion) to this variable.  
Fix by making it `thread_local`.  
  
Fixes #189

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2024-12-20 12:39:29 UTC  
> Updated_at: 2024-12-20 13:36:21 UTC  
> Url: https://github.com/boostorg/nowide/pull/191#issuecomment-2556929361  

## [Codecov](https://app.codecov.io/gh/boostorg/nowide/pull/191?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
All modified and coverable lines are covered by tests :white_check_mark:  
> Project coverage is 99.47%. Comparing base [(`1423d15`)](https://app.codecov.io/gh/boostorg/nowide/commit/1423d15ba54fa14063fdeab496f0345323920c14?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) to head [(`b9ff85d`)](https://app.codecov.io/gh/boostorg/nowide/commit/b9ff85d99a4d39ef656a75436cd3c4c635664566?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> Report is 5 commits behind head on develop.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
```diff  
@@           Coverage Diff            @@  
##           develop     #191   +/-   ##  
========================================  
  Coverage    99.47%   99.47%             
========================================  
  Files           34       34             
  Lines         3460     3460             
========================================  
  Hits          3442     3442             
  Misses          18       18             
```  
  
| [Files with missing lines](https://app.codecov.io/gh/boostorg/nowide/pull/191?dropdown=coverage&src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [src/cstdlib.cpp](https://app.codecov.io/gh/boostorg/nowide/pull/191?src=pr&el=tree&filepath=src%2Fcstdlib.cpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-c3JjL2NzdGRsaWIuY3Bw) | `91.66% <100.00%> (ø)` | |  
  
</details>

---
