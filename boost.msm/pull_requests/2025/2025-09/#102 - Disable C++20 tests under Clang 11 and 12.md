# #102 Disable C++20 tests under Clang 11 and 12 [Merged]

> Username: pdimov  
> Created at: 2025-09-14 14:14:43 UTC  
> Updated at: 2025-10-27 20:59:29 UTC  
> Merged at: 2025-09-15 20:26:32 UTC  
> Closed at: 2025-09-15 20:26:32 UTC  
> Url: https://github.com/boostorg/msm/pull/102  

Clang 11 and 12 don't support lambdas in unevaluated contexts, so the C++20 tests fail for them.

---
