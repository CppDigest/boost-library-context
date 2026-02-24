# #87 Merge Gauss Kronrod code into develop. [Merged]

> Username: jzmaddock  
> Created at: 2017-09-06 18:59:37 UTC  
> Updated at: 2018-02-09 23:35:39 UTC  
> Merged at: 2017-09-13 15:57:07 UTC  
> Closed at: 2017-09-13 15:57:07 UTC  
> Url: https://github.com/boostorg/math/pull/87  



---

## Comment 1

> Username: NAThompson  
> Created_at: 2017-09-06 20:15:15 UTC  
> Url: https://github.com/boostorg/math/pull/87#issuecomment-327600289  

The problem with the Chebyshev code is that we must have a discrete cosine transform to make it useful. The use case is to take a complicated function, and project it onto the Chebyshev polynomials, which have simple formulas for evaluation, integration, and differentiation, giving a numerical integrator, numerical differentiator, and interpolator. The projection requires the DCT to be efficient.  
  
So I guess the question is: Does boost math need a DCT?

---

## Comment 2

> Username: NAThompson  
> Created_at: 2017-09-06 20:16:49 UTC  
> Url: https://github.com/boostorg/math/pull/87#issuecomment-327600675  

Note: Trefethen's 'Chebfun' package is the canonical implementation of these algorithms.

---
