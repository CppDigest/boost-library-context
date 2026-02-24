# #3 Fix UnconsumedResultTest on modern compilers [Closed]

> Username: MarcelRaad  
> Created at: 2015-03-03 11:45:34 UTC  
> Updated at: 2017-11-21 08:32:03 UTC  
> Closed at: 2017-11-20 23:23:12 UTC  
> Url: https://github.com/boostorg/statechart/pull/3  

This test relied on a destructor throwing an exception in debug mode, which crashed the program on modern compilers. Now the custom assert handler for the regression tests increments a counter instead of throwing an exception.  
  
Tested on Visual C++ 14 CTP6.  
  
The failing tests are the four UnconsumedResultTest\* on msvc-14.0, clang- darwin-11, clang- darwin-14, and all the "BP x86_64 C++11" testers:  
http://www.boost.org/development/tests/develop/developer/statechart.html

---

## Comment 1

> Username: pdimov  
> Created_at: 2017-11-20 23:23:12 UTC  
> Url: https://github.com/boostorg/statechart/pull/3#issuecomment-345864911  

Fixed in https://github.com/boostorg/statechart/commit/7be5ff299529257153839c13c2c8f57da33b43c2 before I noticed this PR.

---
