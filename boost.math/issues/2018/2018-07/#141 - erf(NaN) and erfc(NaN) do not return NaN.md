# #141 - erf(NaN) and erfc(NaN) do not return NaN [Closed]

> Username: jzmaddock  
> Created at: 2018-07-31 17:56:32 UTC  
> Updated at: 2018-08-19 17:57:17 UTC  
> Closed at: 2018-08-19 17:57:17 UTC  
> Url: https://github.com/boostorg/math/issues/141  

It is expected that erf(NaN) and erfc(NaN) are NaN, but they are not:  
```  
#include <iostream>  
#include <cmath>  
#include "boost/math/special_functions/erf.hpp"  
  
int main(){  
    std::cout << boost::math::erf(nan("")) << "\n";  
    std::cout << boost::math::erfc(nan("")) << "\n";  
    return 0;  
}  
```  
Output:  
```  
1  
0  
```  
  
Moved from Trac
