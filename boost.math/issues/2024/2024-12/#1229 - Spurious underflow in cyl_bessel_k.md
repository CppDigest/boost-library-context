# #1229 - Spurious underflow in cyl_bessel_k [Open]

> Username: jzmaddock  
> Created at: 2024-12-23 13:26:02 UTC  
> Updated at: 2024-12-23 13:26:02 UTC  
> Url: https://github.com/boostorg/math/issues/1229  

`cyl_bessel_k(100, 746)`  returns zero when evaluating at double precision (with no extended long double type), but the result should be 9.49147e-66.  
  
Underflow occurs in k0 and k1 prior to iteration.  The problem is also present if v is perturbed to be a non-integer in which case CF2_ik underflows.  
  
Reduced test case from https://github.com/boostorg/math/pull/1228.
