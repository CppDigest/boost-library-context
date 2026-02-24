# #142 - Noncentral chi-squared distribution with 0 degrees of freedom [Closed]

> Username: jzmaddock  
> Created at: 2018-07-31 17:58:22 UTC  
> Updated at: 2018-08-19 17:13:33 UTC  
> Closed at: 2018-08-19 17:13:32 UTC  
> Url: https://github.com/boostorg/math/issues/142  

Boost returns an error when we supply 0 for the degrees of freedom of chi-squared distribution:  
```  
#include <iostream>  
#include <cmath>  
#include "boost/math/distributions/non_central_chi_squared.hpp"  
  
int main(){  
    std::cout << boost::math::cdf(boost::math::non_central_chi_squared(0, 1), 1);  
    return 0;  
}  
```  
Error in function boost::math::non_central_chi_squared_distribution<double>::non_central_chi_squared_distribution(double,double): Degrees of freedom argument is 0, but must be > 0 ''  
  
However this distribution makes sense. It has a density.   
  
Moved from Trac.

---

## Comment 1

> Username: jzmaddock  
> Created at: 2018-08-19 17:13:32 UTC  
> Url: https://github.com/boostorg/math/issues/142#issuecomment-414141693  

I'm not going to fix this because:  
  
1) It's a fair bit of work to implement.  
2) The zero degrees of freedom case is actually quite odd - CDF has a reasonable formula but density does not - indeed the density does not even add up to unity!  
3) It's not clear that many (any?) of the other functions can be implemented in that case.  
  
I actually think this is a separate distribution in it's own right - though quite what to call it is another matter.  Submissions, PR's etc welcome.  
  
The reference for this is:  
  
 The Noncentral Chi-Squared Distribution with Zero Degrees of Freedom and Testing for Uniformity  
Andrew F. Siegel  
Biometrika  
Vol. 66, No. 2 (Aug., 1979), pp. 381-386
