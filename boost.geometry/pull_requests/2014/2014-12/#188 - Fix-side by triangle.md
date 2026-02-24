# #188 Fix/side by triangle [Closed]

> Username: awulkiew  
> Created at: 2014-12-27 15:10:22 UTC  
> Updated at: 2014-12-27 22:01:28 UTC  
> Closed at: 2014-12-27 22:01:14 UTC  
> Url: https://github.com/boostorg/geometry/pull/188  

This PR fixes an issue related to the calculation of sides for very small segments. For segments containing endpoints very close to each other but still further than machine epsilon and the checked Point close to them (see the test).  
  
In such cases the segment is not degenerated, the collinearity is checked in cart_intersect by calculating the determinant which subtracts squared differences of segments. When very small numbers are squared the result is even smaller (smaller than epsilon). Until now all numbers smaller than 1 was compared using epsilon (`1.0` passed into `max()`) so the result of collinearity check is `true` even if the points aren't collinear. In the result `relate_collinear` is called and ratios with `denominator==0` can be created which causes assert failure in `less`.  
  
This PR changes the handling of such numbers by using absolute error as suggested here: https://randomascii.wordpress.com/2012/02/25/comparing-floating-point-numbers-2012-edition/ however the value of the error is not passed explicitly but calculated using passed ErrorCalc and machine epsilon.

---

## Comment 1

> Username: awulkiew  
> Created_at: 2014-12-27 22:01:14 UTC  
> Url: https://github.com/boostorg/geometry/pull/188#issuecomment-68191488  

This fix doesn't play well with other parts of the library, e.g. self-intersection checks and in general is very "invasive". Closing.

---
