# #1416 Fix windows runners in github actions [Merged]

> Username: vissarion  
> Created at: 2025-07-17 09:05:01 UTC  
> Updated at: 2025-07-18 15:41:25 UTC  
> Merged at: 2025-07-18 15:41:22 UTC  
> Closed at: 2025-07-18 15:41:22 UTC  
> Url: https://github.com/boostorg/geometry/pull/1416  

This PR removes the retired windows-2019 runner together with msvc-14.2 toolset.   
  
Also adds support for newer runner windows-2025 which gives access to  msvc-14.4 toolset but b2 returns an error so this case is commented out for now (needs more investigation). Related issue https://github.com/boostorg/boost/issues/914

---

## Review 1 [Approved]

> Username: barendgehrels  
> Created_at: 2025-07-18 15:39:59 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/geometry/pull/1416#pullrequestreview-3034011839  

Thanks and looks also as a nice smplification

---
