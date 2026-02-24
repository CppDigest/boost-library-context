# #166 Fix potential macro redefinition in test_traits [Merged]

> Username: Flamefire  
> Created at: 2022-09-27 10:31:21 UTC  
> Updated at: 2022-09-27 15:39:15 UTC  
> Merged at: 2022-09-27 15:39:11 UTC  
> Closed at: 2022-09-27 15:39:12 UTC  
> Url: https://github.com/boostorg/nowide/pull/166  

Check that `_SILENCE_EXPERIMENTAL_FILESYSTEM_DEPRECATION_WARNING` is not defined already before defining it to avoid a warning (e.g. C4005 on MSVC)  
  
Fixes #165

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2022-09-27 13:28:38 UTC  
> Updated_at: 2022-09-27 13:32:57 UTC  
> Url: https://github.com/boostorg/nowide/pull/166#issuecomment-1259510368  

# [Codecov](https://codecov.io/gh/boostorg/nowide/pull/166?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#166](https://codecov.io/gh/boostorg/nowide/pull/166?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (f24db9a) into [develop](https://codecov.io/gh/boostorg/nowide/commit/fed43a678bf29e1e1e3a342e724961d198a3ac37?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (fed43a6) will **not change** coverage.  
> The diff coverage is `n/a`.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
```diff  
@@           Coverage Diff            @@  
##           develop     #166   +/-   ##  
========================================  
  Coverage    99.46%   99.46%             
========================================  
  Files           33       33             
  Lines         3387     3387             
========================================  
  Hits          3369     3369             
  Misses          18       18             
```  
  
| [Impacted Files](https://codecov.io/gh/boostorg/nowide/pull/166?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [test/test\_traits.cpp](https://codecov.io/gh/boostorg/nowide/pull/166/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC90ZXN0X3RyYWl0cy5jcHA=) | `100.00% <ø> (ø)` | |  
  
</details>

---
