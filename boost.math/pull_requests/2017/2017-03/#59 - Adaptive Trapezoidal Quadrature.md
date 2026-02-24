# #59 Adaptive Trapezoidal Quadrature [Merged]

> Username: NAThompson  
> Created at: 2017-03-06 01:12:36 UTC  
> Updated at: 2018-02-10 01:05:49 UTC  
> Merged at: 2017-06-17 07:14:19 UTC  
> Closed at: 2017-06-17 07:14:19 UTC  
> Url: https://github.com/boostorg/math/pull/59  

This routine estimates the definite integral of a function f.  
Assuming that f is periodic, it can be shown that this routine converges exponentially fast.  
In fact, the test cases given exhibit exponential convergence with decreasing stepsize.  
  
A potential improvement is using the Bulirsch sequence rather than the Romberg sequence to schedule the refinements.  
However, the convergence is so rapid for functions of the class specified above that there seems to be no need at present.  
  
This code is cppcheck clean, and runs successfully under AddressSanitizer and UndefinedBehaviorSanitizer.

---

## Comment 1

> Username: NAThompson  
> Created_at: 2017-05-02 18:30:18 UTC  
> Url: https://github.com/boostorg/math/pull/59#issuecomment-298721062  

This should be ready to go.

---

## Comment 2

> Username: NAThompson  
> Created_at: 2017-05-18 23:36:13 UTC  
> Url: https://github.com/boostorg/math/pull/59#issuecomment-302569610  

Looks like I've broke the "test/compile_test/poison.hpp" test by adding 'std::isfinite' to the concept list. However, without adding this, the trapezoidal concept test doesn't pass, so I'm a bit flummoxed here.

---

## Comment 3

> Username: jzmaddock  
> Created_at: 2017-06-16 17:39:57 UTC  
> Url: https://github.com/boostorg/math/pull/59#issuecomment-309088740  

I've moved this to https://github.com/boostorg/math/pull/70 so that we can trigger a CI build, that also contains a bunch of minor fixes for older compilers.

---
