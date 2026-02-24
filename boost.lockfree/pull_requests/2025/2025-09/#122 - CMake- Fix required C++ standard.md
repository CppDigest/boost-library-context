# #122 CMake: Fix required C++ standard [Merged]

> Username: Flamefire  
> Created at: 2025-09-06 10:23:36 UTC  
> Updated at: 2025-09-07 09:12:23 UTC  
> Merged at: 2025-09-06 14:10:39 UTC  
> Closed at: 2025-09-06 14:10:39 UTC  
> Url: https://github.com/boostorg/lockfree/pull/122  

cxx_std_14 is only available since CMake 3.8  
The CXX_STANDARD_REQUIRED property is a boolean, i.e. ON or OFF not a numeric value. The feature is enough to require the standard already  
  
In the test the specification is not required as the library sets it

---

## Comment 1

> Username: timblechmann  
> Created_at: 2025-09-06 14:10:45 UTC  
> Url: https://github.com/boostorg/lockfree/pull/122#issuecomment-3262247852  

thanks!

---
