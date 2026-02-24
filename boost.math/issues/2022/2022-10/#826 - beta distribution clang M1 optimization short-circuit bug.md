# #826 - beta distribution clang M1 optimization short-circuit bug [Closed]

> Username: mckib2  
> Created at: 2022-10-02 21:45:24 UTC  
> Updated at: 2022-10-14 02:28:41 UTC  
> Closed at: 2022-10-12 22:03:14 UTC  
> Url: https://github.com/boostorg/math/issues/826  

xref https://github.com/scipy/scipy/issues/14901  
  
When built with optimization level >1, Clang on Apple M1 platforms appears to generate incorrect code that does not short-circuit the following if statement from [igamma_inverse.hpp](https://github.com/boost-cmake/boost/blob/527022e770e6fe217d849e72857e84e1610ab89f/libs/math/include/boost/math/special_functions/detail/igamma_inverse.hpp#L365):  
  
```cpp  
      if((fabs(div) > 1) && (tools::max_value<T>() / fabs(div) < f2))  
```  
  
leading to a floating point exception as the right side will overflow if evaluated.  This was observed through the Beta distribution with particular ranges of values (see an example below).  
  
Reproducer built with `-O2` on M1 Mac with clang:  
  
```  
% clang --version  
Apple clang version 14.0.0 (clang-1400.0.29.102)  
Target: arm64-apple-darwin21.6.0  
Thread model: posix  
InstalledDir: /Library/Developer/CommandLineTools/usr/bin  
```  
  
```cpp  
#include <iostream>  
#include <cfenv>  
  
#include "boost/math/distributions/beta.hpp"  
  
#pragma STDC FENV_ACCESS ON  
  
int main() {  
    constexpr double q {0.999995};  
    constexpr double a {2};  
    constexpr double b {99999};  
    std::feclearexcept(FE_ALL_EXCEPT);  
    boost::math::beta_distribution<double> d {a, b};  
    const auto ans = boost::math::quantile(d, q);  
    if(std::fetestexcept(FE_OVERFLOW)) {  
        std::cout << "overflow reported" << std::endl;  
    } else {  
        std::cout << "overflow not reported" << std::endl;  
    }  
    std::cout << std::setprecision(16) << "Ans is: " << ans << std::endl;  
    return 0;  
}  
// overflow reported  
// Ans is: 0.000149761910560502  
```  
  
Overflow is not reported when built with `-O1` or when building with Mac Intel (or any other platform really).

---

## Comment 1

> Username: mckib2  
> Created at: 2022-10-14 02:28:41 UTC  
> Url: https://github.com/boostorg/math/issues/826#issuecomment-1278388615  

Thanks @mborland!
