# #12 Use non-NULL sentinel for boost::throws() [Closed]

> Username: visigoth  
> Created at: 2017-01-14 19:57:05 UTC  
> Updated at: 2017-09-07 20:40:45 UTC  
> Closed at: 2017-09-07 20:40:45 UTC  
> Url: https://github.com/boostorg/system/pull/12  

The contract for `boost::throws()` requires a function that advertises compliance to check whether the provided error code object is equivalent to the object represented by `boost::throws()`. Because `boost::throws()` is inlined, and because it returns a reference type, clang optimized this away in `clock_clock_p_d` because it is a reference being tested against `NULL`. The C++ spec states that references must be initialized, so the compiler is at liberty to make this optimization. This worked with the deprecated logic because there was a linked symbol to test. Replacing this with a non-NULL sentinel value allows this to work.

---

## Comment 1

> Username: Beman  
> Created_at: 2017-09-06 14:48:13 UTC  
> Url: https://github.com/boostorg/system/pull/12#issuecomment-327507630  

This change makes me nervous because it doesn't appear to have any test coverage.  I'll try to add at least a minimal test.  
  
Thanks for your patience,  
  
--Beman

---

## Comment 2

> Username: Beman  
> Created_at: 2017-09-07 20:40:45 UTC  
> Url: https://github.com/boostorg/system/pull/12#issuecomment-327921604  

Fix applied to develop after first adding test cases to error_code_test.cpp. Here is the commit message "Hand added the fix from github.com/boostorg/system/pull/12. Note: This fix was verified by testing in release mode with clang 4.0 on Linux. It crashed before applying the P/R and passed afterwards. The pull request was applied by hand to add some new comments and do some other minor code rearrangements. Thanks to Visigoth for the P/R."  
  
Thanks,  
  
--Beman

---
