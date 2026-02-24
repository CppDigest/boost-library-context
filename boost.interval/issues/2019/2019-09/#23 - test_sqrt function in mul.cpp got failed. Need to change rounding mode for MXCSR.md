# #23 - test_sqrt function in mul.cpp got failed. Need to change rounding mode for MXCSR. [Open]

> Username: yuxianch  
> Created at: 2019-09-16 06:44:58 UTC  
> Updated at: 2019-09-20 00:36:06 UTC  
> Url: https://github.com/boostorg/interval/issues/23  

Code to reproduce:    
  
> #include <boost/numeric/interval.hpp>  
#include <boost/test/minimal.hpp>  
#include "bugs.hpp"  
typedef boost::numeric::interval<double> I;  
static bool test_sqrt(double al, double au) {  
I a(al, au);  
I b = square(sqrt(a));  
return subset(abs(a), b);  
}  
int test_main(int, char*[]) {  
BOOST_CHECK(test_sqrt(2, 3));  
BOOST_CHECK(test_sqrt(5, 7));  
return 0;  
}  
  
When linking with -limf(Intel's LIBM Math library), this test would got failed when running. Below is the error message.  
  
> test.cpp(15): test test_sqrt(2, 3) failed in function: 'int test_main(int, char **)'  
test.cpp(16): test test_sqrt(5, 7) failed in function: 'int test_main(int, char **)'  
**** 2 errors detected  
  
This test changed rounding mode only on x87 FPU control register (bits 10 and 11) but not on MXCSR register (bits 13 and 14). There are functions in LIBM that could use x87 or simd instructions. In this case, SSE version of sqrt is called and it uses "sqrtsd %xmm0,%xmm0" instruction. This sqrtsd instruction uses the rounding mode that is in the MXCSR. Therefore, the rounding bits in MXCSR should be updated as well.

---

## Comment 1

> Username: yuxianch  
> Created at: 2019-09-20 00:36:06 UTC  
> Url: https://github.com/boostorg/interval/issues/23#issuecomment-533358175  

Additional info: We have fesetround function (see in fenv.h) to set rounding modes in both x87 control register and MXCSR. It is probably easier to use it. We have also fegetround but it returns Rounding Mode bits from MXCSR in Linux.
