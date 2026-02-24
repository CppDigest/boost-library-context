# #85 - Refactor tests to test multiple variations of backends [Closed]

> Username: chandryan  
> Created at: 2025-03-09 15:42:53 UTC  
> Updated at: 2025-09-08 18:47:44 UTC  
> Closed at: 2025-09-08 18:47:44 UTC  
> Url: https://github.com/boostorg/msm/issues/85  

Hi,  
  
I would like to contribute to MSM with a refactoring of the existing tests, so the same test code can be re-used to test all variations of the available backends.  
  
In #84 I have prepared a PR that unifies all existing tests and adds test cases for yet not existing variants.  
The refactored tests now cover the following backend variations:  
  
- `back` default  
- `back` with `favor_compile_time`  
- `back11` default  
  
Other insights worth sharing:  
  
- A little refactoring of the production sources was necessary to be able to use both `back` and `back11` backends in one CU  
- I identified one test case `OrthogonalDeferredEuml` that seems to fail on `back11`. I'll create another issue to keep track of it  
  
During my work on a new backend with Mp11 I realized such a refactoring would be a necessary prerequisite to keep the tests maintainable and additionally increase overall test coverage 😄   
  
  
Looking forward to hear your feedback :-)

---

## Comment 1

> Username: chandryan  
> Created at: 2025-09-08 18:47:44 UTC  
> Url: https://github.com/boostorg/msm/issues/85#issuecomment-3267494397  

Closing issue, the refactored tests have been merged with https://github.com/boostorg/msm/pull/96
