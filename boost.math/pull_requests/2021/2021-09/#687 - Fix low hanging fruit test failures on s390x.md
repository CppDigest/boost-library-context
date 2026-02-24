# #687 Fix low hanging fruit test failures on s390x. [Merged]

> Username: jzmaddock  
> Created at: 2021-09-04 09:47:18 UTC  
> Updated at: 2021-10-24 15:51:52 UTC  
> Merged at: 2021-10-24 12:03:22 UTC  
> Closed at: 2021-10-24 12:03:22 UTC  
> Url: https://github.com/boostorg/math/pull/687  

This adds some extra instrumentation to bessel_j0.hpp, everything else are fixes for the test programs.  
The advantage of testing on this platform is that it has a true 128-bit long double which is a good test of our assumptions in code.

---
