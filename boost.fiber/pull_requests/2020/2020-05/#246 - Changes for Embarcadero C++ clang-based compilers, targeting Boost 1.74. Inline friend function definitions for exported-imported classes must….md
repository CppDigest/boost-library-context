# #246 Changes for Embarcadero C++ clang-based compilers, targeting Boost 1.74. Inline friend function definitions for exported/imported classes must… [Merged]

> Username: eldiener  
> Created at: 2020-05-20 00:43:03 UTC  
> Updated at: 2020-07-17 05:54:48 UTC  
> Merged at: 2020-07-17 05:54:47 UTC  
> Closed at: 2020-07-17 05:54:48 UTC  
> Url: https://github.com/boostorg/fiber/pull/246  

… become declarations and inline definitions outside the class for Embarcadero C++ clang-based compilers. This bug has been reported to Embarcadero.

---

## Comment 1

> Username: eldiener  
> Created_at: 2020-05-21 07:31:06 UTC  
> Url: https://github.com/boostorg/fiber/pull/246#issuecomment-631932961  

The test failures are because of weaknesses of the clang implementations used in the testing, and have nothing to do with this PR. Please merge this PR.

---
