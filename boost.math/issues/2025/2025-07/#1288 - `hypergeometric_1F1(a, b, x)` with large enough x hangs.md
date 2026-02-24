# #1288 - `hypergeometric_1F1(a, b, x)` with large enough x hangs. [Open]

> Username: WarrenWeckesser  
> Created at: 2025-07-21 22:24:04 UTC  
> Updated at: 2025-07-22 16:22:59 UTC  
> Url: https://github.com/boostorg/math/issues/1288  

With the `develop` branch, the call `hypergeometric_1F1(2.0, 3.0, 1e30)` never returns.  With smaller (but still "large") values of `x`, e.g. `hypergeometric_1F1(2.0, 3.0, 1e10)`, the expected `overflow_error` exception is thrown.  
  
(I originally found this problem when calling boost/math from SciPy, and verified that a C++ program that used the `develop` branch also has the problem.)

---

## Comment 1

> Username: WarrenWeckesser  
> Created at: 2025-07-22 00:44:56 UTC  
> Updated at: 2025-07-22 16:22:59 UTC  
> Url: https://github.com/boostorg/math/issues/1288#issuecomment-3100160945  

I think the problem is in the implementation of [`hypergeometric_1F1_imp(const T& a, const T& b, const T& z, const Policy& pol)`](https://github.com/boostorg/math/blob/36985826ebd75dcf1eb9670fd5c968d0a43bf591/include/boost/math/special_functions/hypergeometric_1F1.hpp#L629) where it processes the result of `hypergeometric_1F1_imp(a, b, z, pol, log_scaling)`.  As the comment there says, "Actual result will be result * e^log_scaling".  There are two loops that iteratively update `result` and `log_scaling`, but they don't consider the possibility of `result` becoming `inf`.  
  
I don't know if this would be the best (or even correct) fix, but a quick fix is to change this line:  
  
https://github.com/boostorg/math/blob/36985826ebd75dcf1eb9670fd5c968d0a43bf591/include/boost/math/special_functions/hypergeometric_1F1.hpp#L640  
  
to  
  
```C++  
      while (!isinf(result) && log_scaling > max_scaling)  
```  
  
*EDIT:* The above "quick fix" was checked with a few examples with the default double promotion policy.  More work is needed to handle the very large arguments when double promotion is disabled.

---

## Comment 2

> Username: jzmaddock  
> Created at: 2025-07-22 12:13:33 UTC  
> Url: https://github.com/boostorg/math/issues/1288#issuecomment-3102452813  

Thanks for the report: I'll look into this, but I have a hunch this is caused by a "feature".
