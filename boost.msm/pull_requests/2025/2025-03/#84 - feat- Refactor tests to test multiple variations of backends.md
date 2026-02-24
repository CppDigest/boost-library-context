# #84 feat: Refactor tests to test multiple variations of backends [Closed]

> Username: chandryan  
> Created at: 2025-03-09 15:30:03 UTC  
> Updated at: 2025-04-15 10:44:17 UTC  
> Closed at: 2025-04-15 10:44:17 UTC  
> Url: https://github.com/boostorg/msm/pull/84  

* Extracted traits into a separate header to include multiple backends at once  
* Refactored tests to test multiple variations of backends with the same test code  
* Added test cases with back::favor_compile_time for existing tests  
* Cleaned up redundant Back11 tests after refactoring

---

## Comment 1

> Username: chandryan  
> Created_at: 2025-04-15 10:44:17 UTC  
> Url: https://github.com/boostorg/msm/pull/84#issuecomment-2804627816  

Closing this PR in favor of https://github.com/boostorg/msm/pull/89, since I will push more changes to the fork's develop branch.

---
