# #734 Automatically generated, fast, robust floating-point predicates (WIP) [Closed]

> Username: tinko92  
> Created at: 2020-07-09 21:54:21 UTC  
> Updated at: 2020-07-28 11:42:50 UTC  
> Closed at: 2020-07-28 11:38:17 UTC  
> Url: https://github.com/boostorg/geometry/pull/734  

EDIT: closed in favor of more reasonably sized PR: https://github.com/boostorg/geometry/pull/739  
  
This pull request contains the first part of my GSoC 2020 project. This code is still work in progress and there might be changes to the interface before the end of GSoC.  
  
The project aims to implement a number of facilities that allow easy creation of fast and robust geometric floating-point predicates, such as, for example, orientation tests (Boost.Geometry currently has a 2d orientation test in the side-strategy). As of writing this, the pull request contains templates to   
  
- automatically derive error bounds for the approximate evaluation of such expressions  
- build static, almost static and semi-static filters from these building blocks  
  
I intend to add  
- generation of functions for the exact evaluation of arbitrary arithmetic floating-point expressions containing +, - and * using expansion arithmetic  
- improvements for the error bound derivation  
- an alternative method for error bound derivation  
- some additional filters  
- automatic detection of reusable interim results between filter stages  
  
The usage of the various templates for the creation of geometric predicates is illustrated in the tests and the example.  
  
This pull request depends on Boost.MP11, which depends on C++11 but has no additional dependencies. The code itself depends on C++14 because of certain constexpr features.

---
