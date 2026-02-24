# #89 feat: Refactor tests to test multiple variations of backends [Closed]

> Username: chandryan  
> Created at: 2025-04-15 10:42:06 UTC  
> Updated at: 2025-10-06 18:16:13 UTC  
> Closed at: 2025-09-07 13:27:23 UTC  
> Url: https://github.com/boostorg/msm/pull/89  

* Extracted traits into a separate header to include multiple backends at once  
* Refactored tests to test multiple variations of backends with the same test code  
* Added test cases with back::favor_compile_time for existing tests  
* Cleaned up redundant Back11 tests after refactoring

---
