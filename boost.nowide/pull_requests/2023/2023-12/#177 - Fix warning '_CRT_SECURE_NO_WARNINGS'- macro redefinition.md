# #177 Fix warning '_CRT_SECURE_NO_WARNINGS': macro redefinition [Merged]

> Username: Jackarain  
> Created at: 2023-12-25 07:16:33 UTC  
> Updated at: 2023-12-26 11:07:57 UTC  
> Merged at: 2023-12-26 11:07:56 UTC  
> Closed at: 2023-12-26 11:07:56 UTC  
> Url: https://github.com/boostorg/nowide/pull/177  



---

## Comment 1

> Username: Flamefire  
> Created_at: 2023-12-25 09:44:25 UTC  
> Url: https://github.com/boostorg/nowide/pull/177#issuecomment-1868893596  

Thanks for the PR. I'm wondering in which situation you encounter this issue. Those files changed are internal source files and the build scripts don't define those defines. So how do you run into the warning?

---

## Comment 2

> Username: Jackarain  
> Created_at: 2023-12-25 11:50:04 UTC  
> Updated_at: 2023-12-25 11:57:54 UTC  
> Url: https://github.com/boostorg/nowide/pull/177#issuecomment-1868947929  

Thanks for reviewing this.  
The issue stems from the `add_definitions(-D_CRT_SECURE_NO_WARNINGS)` used in main projects, causing a macro redefinition warning with your files. My PR aims to resolve this for smoother integration.  
  
https://github.com/Jackarain/proxy/actions/runs/7319843765/job/19938178701#step:3:1403

---

## Comment 3

> Username: codecov[bot]  
> Created_at: 2023-12-26 10:13:30 UTC  
> Url: https://github.com/boostorg/nowide/pull/177#issuecomment-1869432401  

## [Codecov](https://app.codecov.io/gh/boostorg/nowide/pull/177?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
All modified and coverable lines are covered by tests :white_check_mark:  
> Comparison is base [(`a9cea73`)](https://app.codecov.io/gh/boostorg/nowide/commit/a9cea7390878c0c4a24d7ba138c4a53a0a89150c?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) 99.47% compared to head [(`3c4e045`)](https://app.codecov.io/gh/boostorg/nowide/pull/177?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) 99.47%.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
```diff  
@@           Coverage Diff            @@  
##           develop     #177   +/-   ##  
========================================  
  Coverage    99.47%   99.47%             
========================================  
  Files           34       34             
  Lines         3447     3447             
========================================  
  Hits          3429     3429             
  Misses          18       18             
```  
  
| [Files](https://app.codecov.io/gh/boostorg/nowide/pull/177?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [src/cstdio.cpp](https://app.codecov.io/gh/boostorg/nowide/pull/177?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-c3JjL2NzdGRpby5jcHA=) | `100.00% <ø> (ø)` | |  
| [src/cstdlib.cpp](https://app.codecov.io/gh/boostorg/nowide/pull/177?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-c3JjL2NzdGRsaWIuY3Bw) | `91.66% <ø> (ø)` | |  
| [test/test\_system.cpp](https://app.codecov.io/gh/boostorg/nowide/pull/177?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC90ZXN0X3N5c3RlbS5jcHA=) | `100.00% <ø> (ø)` | |  
  
</details>

---
