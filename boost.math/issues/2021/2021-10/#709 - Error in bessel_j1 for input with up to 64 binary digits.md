# #709 - Error in bessel_j1 for input with up to 64 binary digits [Closed]

> Username: aherbert  
> Created at: 2021-10-19 15:36:00 UTC  
> Updated at: 2021-10-19 17:55:27 UTC  
> Closed at: 2021-10-19 17:43:40 UTC  
> Url: https://github.com/boostorg/math/issues/709  

File: boost/math/special_functions/detail/bessel_j1.hpp  
  
The method bessel_j1 does not use the absolute value of the input argument x when abs(x) is above 8 to compute the sin and cosine.  
  
The function is optimised for input arguments with up to 64-bits of precision. I cannot find a way to call this method via the standard API so it may no longer be used. Here is a program that calls it directly. When abs(x) is over 8 then the function does not behave as an odd function: f(x) = -f(-x):  
  
```c++  
#include <cstdio>  
#include <cmath>  
  
#include <boost/math/special_functions/bessel.hpp>  
#include <boost/math/special_functions/detail/bessel_j1.hpp>  
  
int main(int argc, const char* argv[]) {  
  for (double x=6; x<=12; x+=0.5) {  
    printf("%5.2f : %8.3f   %8.3f : %8.3f   %8.3f\n",  
           x,  
           // Current implementation  
           boost::math::cyl_bessel_j(1, x),  
           boost::math::cyl_bessel_j(1, -x),  
           // 64-bit double implementation  
           boost::math::detail::bessel_j1(x),  
           // This is wrong when |x| > 8  
           boost::math::detail::bessel_j1(-x));  
  }  
}  
```  
Outputs:  
```  
 6.00 :   -0.277      0.277 :   -0.277      0.277  
 6.50 :   -0.154      0.154 :   -0.154      0.154  
 7.00 :   -0.005      0.005 :   -0.005      0.005  
 7.50 :    0.135     -0.135 :    0.135     -0.135  
 8.00 :    0.235     -0.235 :    0.235     -0.235  
 8.50 :    0.273     -0.273 :    0.273      0.050  
 9.00 :    0.245     -0.245 :    0.245     -0.084  
 9.50 :    0.161     -0.161 :    0.161     -0.190  
10.00 :    0.043     -0.043 :    0.043     -0.245  
10.50 :   -0.079      0.079 :   -0.079     -0.239  
11.00 :   -0.177      0.177 :   -0.177     -0.175  
11.50 :   -0.228      0.228 :   -0.228     -0.073  
12.00 :   -0.223      0.223 :   -0.223      0.043  
```  
  
The first two result columns are as expected. The result with -x should be a negation of the result with x. The next two have a mismatch when x > 8.  
  
This can be fixed by changing:  
```c++  
        T sx = sin(x);  
        T cx = cos(x);  
```  
to  
```c++  
        // w = abs(x)  
        T sx = sin(w);  
        T cx = cos(w);  
```

---

## Comment 1

> Username: jzmaddock  
> Created at: 2021-10-19 17:43:40 UTC  
> Url: https://github.com/boostorg/math/issues/709#issuecomment-946954889  

bessel_j1 is an implementation detail, and is only used for x > 0.  cyl_bessel_j will call it internally in this case after having first applied the usual reflection formulas.

---

## Comment 2

> Username: aherbert  
> Created at: 2021-10-19 17:55:27 UTC  
> Url: https://github.com/boostorg/math/issues/709#issuecomment-946963905  

Thank you for the clarification.
