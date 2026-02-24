# #922 - CDF and PMF of binomial function not same with extreme values [Closed]

> Username: mborland  
> Created at: 2023-01-19 16:40:43 UTC  
> Updated at: 2023-01-19 18:07:33 UTC  
> Closed at: 2023-01-19 18:07:33 UTC  
> Url: https://github.com/boostorg/math/issues/922  

See: https://github.com/scipy/scipy/issues/17809

---

## Comment 1

> Username: jzmaddock  
> Created at: 2023-01-19 17:33:24 UTC  
> Url: https://github.com/boostorg/math/issues/922#issuecomment-1397355183  

This may not be us:  
  
```  
#include <boost/math/distributions.hpp>  
#include <vector>  
#include <iostream>  
  
int main()   
{  
   double n = 25.0e21;  
   double p = 1.0e-21;  
   double r = 0;  
  
   boost::math::binomial_distribution<double> d(n, p);  
  
   std::cout << pdf(d, r) << std::endl;  
   std::cout << cdf(d, r) << std::endl;  
  
   return 0;  
}  
```  
  
Outputs:  
  
```  
1.38879e-11  
1.38879e-11  
```  
  
Or have I misread the OP?

---

## Comment 2

> Username: mborland  
> Created at: 2023-01-19 17:36:42 UTC  
> Url: https://github.com/boostorg/math/issues/922#issuecomment-1397359563  

I don't think this is us now that you confirmed. I got the same results with the following.  
  
```  
#include <boost/math/distributions/binomial.hpp>  
#include "math_unit_test.hpp"  
  
// See: https://github.com/scipy/scipy/issues/17809  
  
int main()  
{  
    constexpr double n {25.0e21};  
    constexpr double p {1.0e-21};  
    constexpr double r {0};  
  
    // In R the PDF and CDF are both 1.388794386496407e-11  
    // In boost 1.81 CDF was 1.388794386496407e-11 but PDF was 1.0  
  
    const auto binom_dist = boost::math::binomial_distribution<double>(n, p);  
  
    CHECK_ULP_CLOSE(1.388794386496407e-11, boost::math::cdf(binom_dist, r), 1);  
    CHECK_ULP_CLOSE(1.388794386496407e-11, boost::math::pdf(binom_dist, r), 1);  
  
    return boost::math::test::report_errors();  
}  
```

---

## Comment 3

> Username: mborland  
> Created at: 2023-01-19 17:45:50 UTC  
> Url: https://github.com/boostorg/math/issues/922#issuecomment-1397373527  

I ran this against master too, and we still get the correct results.

---

## Comment 4

> Username: mborland  
> Created at: 2023-01-19 18:07:33 UTC  
> Url: https://github.com/boostorg/math/issues/922#issuecomment-1397401287  

Confirmed this fails using 1.80 but was fixed in 1.81. Scipy will update their packaged version of Boost.Math for next release.
