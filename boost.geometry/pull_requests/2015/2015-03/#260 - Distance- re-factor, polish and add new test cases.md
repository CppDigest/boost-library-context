# #260 Distance: re-factor, polish and add new test cases [Merged]

> Username: mkaravel  
> Created at: 2015-03-11 08:55:59 UTC  
> Updated at: 2015-03-11 14:23:06 UTC  
> Merged at: 2015-03-11 14:23:06 UTC  
> Closed at: 2015-03-11 14:23:06 UTC  
> Url: https://github.com/boostorg/geometry/pull/260  

In this PR:  
- The common distance testing code has been re-factored and polished.  
- It is now allowed to check test cases where the distance computed is not a finite floating-point number.  
- Added a test case where the distance overflows.  
- Added a test case where the distance computed is a `NaN`.

---
