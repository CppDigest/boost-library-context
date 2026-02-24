# #246 - Reinsch's Modification to Clenshaw Recurrence [Closed]

> Username: NAThompson  
> Created at: 2019-08-25 18:00:16 UTC  
> Updated at: 2020-01-13 17:05:59 UTC  
> Closed at: 2020-01-13 17:05:49 UTC  
> Url: https://github.com/boostorg/math/issues/246  

The special functions of Boost.Math generally allow point evaluation of the function and perhaps their derivatives. This is useful for a large number of tasks. However, in order to make these building blocks more useful, stable evaluation of series of special functions could be provided. This would allow people to solve ODEs in (say) Gegenbauer polynomials and solve a matrix equation for the coefficients, and then evaluate the solution and all its derivatives rapidly. We already have this for Chebyshev series, but I suggest adding it to all special functions which obey a three-term recurrence relation. This can be done via Reinsch's modification to the Clenshaw recurrence:  
  
https://doi.org/10.1093/imamat/20.3.379  
  
I believe John already has a building block for this task with his three term recurrence generator in `tools`.

---

## Comment 1

> Username: NAThompson  
> Created at: 2020-01-13 17:05:59 UTC  
> Url: https://github.com/boostorg/math/issues/246#issuecomment-573767614  

Closed as moved to wishlist.
