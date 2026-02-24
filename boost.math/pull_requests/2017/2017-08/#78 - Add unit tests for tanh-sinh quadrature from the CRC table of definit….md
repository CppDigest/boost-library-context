# #78 Add unit tests for tanh-sinh quadrature from the CRC table of definit… [Closed]

> Username: NAThompson  
> Created at: 2017-08-06 20:08:59 UTC  
> Updated at: 2018-02-09 23:36:22 UTC  
> Closed at: 2017-08-15 18:14:04 UTC  
> Url: https://github.com/boostorg/math/pull/78  

…e integrals, section 5.5. These integrals are interesting because you can control the strength of the singularity at the endpoints by varying a parameter.

---

## Comment 1

> Username: jzmaddock  
> Created_at: 2017-08-15 18:14:03 UTC  
> Url: https://github.com/boostorg/math/pull/78#issuecomment-322545255  

I'm not sure why this hasn't closed itself, but this is now in PR #72.  
  
I could have sworn I'd commented on these new tests before as well.... but basically these have issues because we simply run out of exponent range - moving to a type with an extended exponent range (even an 80-bit long double) addresses the non-convergence issues.  A couple of alternatives are presented in the documentation, and in the tests as well: we can either use argument substitution to transform the integral over a wider range, and into a form that lets us evaluate the function with logs, or we can approximate near the singularity by something simpler (a variation of the "subtract out the singularity" method).

---
