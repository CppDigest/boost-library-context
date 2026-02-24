# #147 Fix missing include [Merged]

> Username: Flamefire  
> Created at: 2022-03-06 11:48:03 UTC  
> Updated at: 2022-03-06 15:33:09 UTC  
> Merged at: 2022-03-06 15:33:03 UTC  
> Closed at: 2022-03-06 15:33:03 UTC  
> Url: https://github.com/boostorg/nowide/pull/147  

Add missing include: `shellapi.h`  
  
`CommandLineToArgvW `is defined in `shellapi.h` not `windows.h` although the former is usually included by the latter unless e.g. `WIN32_LEAN_AND_MEAN `is defined.

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2022-03-06 13:51:48 UTC  
> Updated_at: 2022-03-06 13:55:44 UTC  
> Url: https://github.com/boostorg/nowide/pull/147#issuecomment-1059966565  

# [Codecov](https://codecov.io/gh/boostorg/nowide/pull/147?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#147](https://codecov.io/gh/boostorg/nowide/pull/147?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (365013b) into [develop](https://codecov.io/gh/boostorg/nowide/commit/a944d2226dd7fce4fca4fe3e17bff71f7fdcfb2b?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (a944d22) will **not change** coverage.  
> The diff coverage is `n/a`.  
  
```diff  
@@           Coverage Diff            @@  
##           develop     #147   +/-   ##  
========================================  
  Coverage    99.33%   99.33%             
========================================  
  Files           33       33             
  Lines         3001     3001             
========================================  
  Hits          2981     2981             
  Misses          20       20             
```  
  
| [Impacted Files](https://codecov.io/gh/boostorg/nowide/pull/147?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [test/test\_system.cpp](https://codecov.io/gh/boostorg/nowide/pull/147/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC90ZXN0X3N5c3RlbS5jcHA=) | `100.00% <ø> (ø)` | |

---
