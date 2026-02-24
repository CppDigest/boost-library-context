# #710 - Update limits in erf.hpp for 53-bit precision [Closed]

> Username: aherbert  
> Created at: 2021-10-21 08:25:42 UTC  
> Updated at: 2021-11-02 19:04:41 UTC  
> Closed at: 2021-11-02 19:04:41 UTC  
> Url: https://github.com/boostorg/math/issues/710  

File: `boost/math/special_functions/erf.hpp`  
  
The upper limit in the 53-bit precision erf_imp for z is set at 5.8f. Above this the output is computed as 1.0. This limit is too low.  
  
The test data in `boost/libs/math/test/erf_data.ipp` has values up to z=5.92 that can be represented as a double. If z=5.81 the function will compute 1.0 which is 2 ulp from the closest representation of the result: 0.99999999999999978.  
  
Test program:  
```c++  
#include <iostream>  
#include <float.h>  
#include <boost/math/special_functions/erf.hpp>  
  
int main(int argc, const char* argv[]) {  
  // From boost/libs/math/test/erf_data.ipp  
  // 5.8028507232666015625,  0.9999999999999997722139370566709142078876  
  // 5.89911556243896484375, 0.9999999999999999273310114171591943570055  
  // 5.90867519378662109375, 0.9999999999999999351899407560267583020934  
  // 5.92298793792724609375, 0.9999999999999999454148146455550046656746  
  // Expected result is 1.0 as a double  
  // 5.95886135101318359375, 0.9999999999999999645670653382102726138283  
  
  for (double i=580; i<=595; i++) {  
    double z = i/100;  
    double p = boost::math::erf(z);  
    long double p1 = boost::math::erf((long double)z);  
    double p2 = (double) p1;  
    printf("%.2f   %20.*g  %28.*Lg  %.*g\n",  
      z,  
      DBL_DECIMAL_DIG, p,  
      LDBL_DECIMAL_DIG, p1,  
      DBL_DECIMAL_DIG, p2);  
  }  
  return 0;  
}  
```  
  
Output:  
  
```  
5.80    0.99999999999999978       0.999999999999999764457  0.99999999999999978  
5.81                      1       0.999999999999999790586  0.99999999999999978  
5.82                      1       0.999999999999999813897  0.99999999999999978  
5.83                      1       0.999999999999999834659  0.99999999999999989  
5.84                      1       0.999999999999999853091  0.99999999999999989  
5.85                      1       0.999999999999999869516  0.99999999999999989  
5.86                      1       0.999999999999999884153  0.99999999999999989  
5.87                      1       0.999999999999999897163  0.99999999999999989  
5.88                      1        0.99999999999999990871  0.99999999999999989  
5.89                      1       0.999999999999999918956  0.99999999999999989  
5.90                      1       0.999999999999999928117  0.99999999999999989  
5.91                      1       0.999999999999999936195  0.99999999999999989  
5.92                      1       0.999999999999999943405  0.99999999999999989  
5.93                      1       0.999999999999999949801  1  
5.94                      1       0.999999999999999955494  1  
5.95                      1       0.999999999999999960589  1  
```  
  
Raising the threshold from 5.8f to at least 5.93 avoids truncation at the upper limit of erf(z).  
  
I have verified the limit of `6073 / 1024 = 5.9306640625f` allows erf to compute the expected result at the upper limit.

---

## Comment 1

> Username: NAThompson  
> Created at: 2021-10-24 02:05:20 UTC  
> Url: https://github.com/boostorg/math/issues/710#issuecomment-950247099  

Just tried to reproduce with this code:  
  
```cpp  
#include <boost/multiprecision/float128.hpp>  
#include <boost/math/special_functions/erf.hpp>  
#include <boost/math/tools/ulps_plot.hpp>  
  
using boost::math::tools::ulps_plot;  
using boost::math::erf;  
using boost::multiprecision::float128;  
  
int main() {  
  double a = 5.92;  
  double b = 5.96;  
  auto f_acc = [](float128 x) { return erf(x); };  
  auto f = [](double x) { return erf(x); };  
  auto plot = ulps_plot<decltype(f_acc), float128, double>(f_acc, a, b);  
  plot.add_fn(f);  
  plot.write("erf_ulps_plot.svg");  
}  
```  
  
Not seeing quite the same thing:  
  
  
<img width="1117" alt="Screen Shot 2021-10-23 at 7 04 19 PM" src="https://user-images.githubusercontent.com/5459618/138577121-fe9aa6d3-6d43-432e-8d43-c8a36dfcd5ef.png">  
  
  
Could you run with this code and see what I did wrong? (Maybe the float128 implementation has the same bug?)

---

## Comment 2

> Username: jzmaddock  
> Created at: 2021-10-24 09:20:52 UTC  
> Url: https://github.com/boostorg/math/issues/710#issuecomment-950290351  

The OP is correct, try:  
  
```  
   double r = 0.5;  
   double arg = 5.8f;  
   arg = boost::math::float_next(arg);  
  
   std::cout << boost::math::float_distance((double)boost::math::erf(boost::multiprecision::cpp_bin_float_50(arg)), boost::math::erf(arg)) << std::endl;  
   std::cout << boost::math::float_distance((double)boost::math::erf(boost::multiprecision::float128(arg)), boost::math::erf(arg)) << std::endl;  
  
   std::cout << boost::math::erfc_inv(std::numeric_limits<double>::epsilon()) << std::endl;  
   std::cout << boost::math::erfc_inv(std::numeric_limits<double>::epsilon() / 2) << std::endl;  
```  
  
outputs:  
  
2  
2  
5.80502  
5.86358  
  
So 5.87 would seem to be a safe limit.  I'll double check the limits on the other approximations as well.

---

## Comment 3

> Username: aherbert  
> Created at: 2021-10-24 12:42:15 UTC  
> Url: https://github.com/boostorg/math/issues/710#issuecomment-950318768  

Thanks for reviewing.  
  
Using the erfc_inv to set the limit achieves a threshold too low. The inverse and error function is not in practice a bijection (due to limited precision). So there are higher values of x to input to erf that can achieve the original value. My original post demonstrates that 5.92 input to the error function is representable as a double. The plot by @NAThompson, if I am reading it correctly, starts at 5.92 with a ulps above 0.5. At around 5.922 the plot drops below the 0.5 threshold.
