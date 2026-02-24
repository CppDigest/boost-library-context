# #453 - Legendre Polynomial Issue with l=0, m=-1 [Closed]

> Username: abhinavsns  
> Created at: 2020-11-07 23:17:28 UTC  
> Updated at: 2020-11-11 19:20:58 UTC  
> Closed at: 2020-11-11 19:20:58 UTC  
> Url: https://github.com/boostorg/math/issues/453  

```  
#include <boost/math/special_functions/legendre.hpp>  
  
int main () {  
  boost::math::legendre_p(0,-1,1.0);  
  return 0;  
}  
```  
  
Results in:  
libc++abi.dylib: terminating with uncaught exception of type boost::wrapexcept<std::domain_error>: Error in function boost::math::tgamma_ratio<long double>(long double, long double): Gamma function ratios only implemented for positive arguments (got a=0).  
Abort trap: 6

---

## Comment 1

> Username: jzmaddock  
> Created at: 2020-11-08 18:37:55 UTC  
> Url: https://github.com/boostorg/math/issues/453#issuecomment-723648971  

Confirmed, we need a special case for `m = l - 1`.  Will fix shortly.
