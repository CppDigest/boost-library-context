# #243 Changes for Embarcadero C++ clang-based compilers, targeting Boost 1.74. Inline friend function definitions for exported/imported classes must… [Closed]

> Username: eldiener  
> Created at: 2020-04-25 19:23:10 UTC  
> Updated at: 2020-05-20 00:16:54 UTC  
> Closed at: 2020-05-20 00:16:54 UTC  
> Url: https://github.com/boostorg/fiber/pull/243  

… become declarations and inline definitions outside the class for Embarcadero C++ clang-based compilers. This bug has been reported to Embarcadero.

---

## Comment 1

> Username: eldiener  
> Created_at: 2020-05-06 04:54:47 UTC  
> Url: https://github.com/boostorg/fiber/pull/243#issuecomment-624440987  

I do not see how my PR has anything to do with the test failures. I believe the test failure would occur on any PR as the test failures are intrinsic to the older versions of compilers being tested.

---
