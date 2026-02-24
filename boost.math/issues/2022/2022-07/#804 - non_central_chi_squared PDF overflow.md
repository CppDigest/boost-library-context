# #804 - non_central_chi_squared PDF overflow [Closed]

> Username: mckib2  
> Created at: 2022-07-19 01:45:14 UTC  
> Updated at: 2022-07-20 16:38:52 UTC  
> Closed at: 2022-07-20 16:38:52 UTC  
> Url: https://github.com/boostorg/math/issues/804  

The `non_central_chi_squared` distribution encounters an overflow during [a calculation in `bessel_ik` (bessel_ik.hpp#L380)](https://github.com/boostorg/math/blob/8f5520cea5168b6fc453945fe2791ae078e5a4c3/include/boost/math/special_functions/detail/bessel_ik.hpp#L380):  
  
```cpp  
        if((tools::max_value<T>() - fabs(prev)) / fact < fabs(current))  
```  
  
Reproducer:  
  
```cpp  
#include <iostream>  
#include "boost/math/distributions/non_central_chi_squared.hpp"  
#include <cfenv>  
  
#pragma STDC FENV_ACCESS ON  
  
using namespace boost::math::policies;  
  
int main() {  
  constexpr int v = 21;  
  constexpr double lambda = 1.0560466;  
  constexpr double x = 21.36270226;  
  boost::math::non_central_chi_squared_distribution<double, policy<promote_double<false> > > ncx2(v, lambda);  
  std::cout << std::setprecision(16) << "ans is " << boost::math::pdf(ncx2, x) << std::endl;  
  if(std::fetestexcept(FE_OVERFLOW)) {  
    std::cout << "overflow reported" << std::endl;  
  } else {  
    std::cout << "overflow not reported" << std::endl;  
  }  
  return 0;  
}  
```  
  
Prints `overflow reported` on current develop branch.
