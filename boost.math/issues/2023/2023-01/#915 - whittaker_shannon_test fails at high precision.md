# #915 - whittaker_shannon_test fails at high precision [Open]

> Username: jzmaddock  
> Created at: 2023-01-10 16:59:29 UTC  
> Updated at: 2023-02-05 16:57:36 UTC  
> Url: https://github.com/boostorg/math/issues/915  

Whittaker_shannon_test fails when long double is a 128 bit type, or when run with either `float128` or `cpp_bin_float_quad`: max errors are of the order 10^-28.    
  
For now I'm disabling running `long double` tests for when `long double` is larger than an 80-bit float just so we can move on and get s390x testing completed, but this should probably be looked at at some point.

---

## Comment 1

> Username: NAThompson  
> Created at: 2023-02-05 16:57:36 UTC  
> Url: https://github.com/boostorg/math/issues/915#issuecomment-1418153593  

So I finally got around to looking at this . . . and I think you've done the correct thing. Those tests are relativized to the unit roundoff of the floating point type, but in reality that's incidental, not fundamental to the test. In order to make it work at all precisions, we'd also have to make the number of knots increase with the precision, but how the number of knots should scale with the precision of the floating point type seems pretty complicated.  
  
In any case, I believe the test needs to be updated with knot numbers that increase with precision, and the pass criteria need to be loosened a bit.
