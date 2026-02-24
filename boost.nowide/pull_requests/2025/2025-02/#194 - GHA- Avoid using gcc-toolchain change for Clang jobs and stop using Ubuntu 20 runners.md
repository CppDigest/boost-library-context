# #194 GHA: Avoid using gcc-toolchain change for Clang jobs and stop using Ubuntu 20 runners [Merged]

> Username: Flamefire  
> Created at: 2025-02-12 07:46:27 UTC  
> Updated at: 2025-02-28 08:30:28 UTC  
> Merged at: 2025-02-28 08:30:09 UTC  
> Closed at: 2025-02-28 08:30:09 UTC  
> Url: https://github.com/boostorg/nowide/pull/194  

Use containers where required to avoid picking up an incompatible libstdc++

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2025-02-27 21:08:29 UTC  
> Updated_at: 2025-02-28 08:30:28 UTC  
> Url: https://github.com/boostorg/nowide/pull/194#issuecomment-2689115704  

## [Codecov](https://app.codecov.io/gh/boostorg/nowide/pull/194?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
All modified and coverable lines are covered by tests :white_check_mark:  
> Project coverage is 99.39%. Comparing base [(`64f1eee`)](https://app.codecov.io/gh/boostorg/nowide/commit/64f1eeed2f3fcb35d61145b48747d6e0a0404ea3?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) to head [(`8f1078a`)](https://app.codecov.io/gh/boostorg/nowide/commit/8f1078ac46f55a838750e07b464f2f1a5a04a0b7?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> Report is 6 commits behind head on develop.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
```diff  
@@           Coverage Diff            @@  
##           develop     #194   +/-   ##  
========================================  
  Coverage    99.39%   99.39%             
========================================  
  Files           34       34             
  Lines         3460     3460             
========================================  
  Hits          3439     3439             
  Misses          21       21             
```  
  
</details>

---
