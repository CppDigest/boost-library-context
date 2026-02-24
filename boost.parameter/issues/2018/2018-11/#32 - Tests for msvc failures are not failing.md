# #32 - Tests for msvc failures are not failing [Closed]

> Username: eldiener  
> Created at: 2018-11-16 23:27:42 UTC  
> Updated at: 2018-11-22 20:29:30 UTC  
> Closed at: 2018-11-22 20:29:30 UTC  
> Url: https://github.com/boostorg/parameter/issues/32  

The tests for parameter_msvc_fail_tests are not failing for msvc-8.0, msvc-9.0, msvc-10.0, and msvc-11.0. These tests are the compile-fail evaluate_category.cpp test and the compile-fail preprocessor_eval_category.cpp test. I am less concerned than usual about these test failures since these are fairly old version of the vc++ compiler, and just may indicate deficiencies in those compilers. But still these test failures should be looked at.

---

## Comment 1

> Username: CromwellEnage  
> Created at: 2018-11-17 16:40:37 UTC  
> Url: https://github.com/boostorg/parameter/issues/32#issuecomment-439630075  

I've changed evaluate_category.cpp and preprocessor_eval_category.cpp so that the tests pass for those compilers.
