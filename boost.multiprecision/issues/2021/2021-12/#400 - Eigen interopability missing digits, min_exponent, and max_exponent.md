# #400 - Eigen interopability missing digits, min_exponent, and max_exponent [Closed]

> Username: FreddieWitherden  
> Created at: 2021-12-06 21:21:47 UTC  
> Updated at: 2021-12-13 09:41:38 UTC  
> Closed at: 2021-12-13 09:41:38 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/400  

The Eigen interoperability trait does not currently define the digits, min_exponent, and max_exponent methods.  However, these are required for norm calculation as can be seen here:  
  
https://gitlab.com/libeigen/eigen/-/blob/master/Eigen/src/Core/StableNorm.h#L138  
  
This results in compiler errors for any code which attempts to compute a norm of an Eigen vector/matrix parameterised by a multiprecision type.

---

## Comment 1

> Username: jzmaddock  
> Created at: 2021-12-09 16:39:24 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/400#issuecomment-990021788  

Looks like this requirement it was introduced in the last release.  
  
Should be fixed, along with better testing in https://github.com/boostorg/multiprecision/pull/402

---

## Comment 2

> Username: jzmaddock  
> Created at: 2021-12-13 09:41:38 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/400#issuecomment-992280423  

Fixed in develop.
