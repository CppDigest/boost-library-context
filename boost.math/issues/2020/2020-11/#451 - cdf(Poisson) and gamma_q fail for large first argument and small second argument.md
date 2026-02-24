# #451 - cdf(Poisson) and gamma_q fail for large first argument and small second argument [Closed]

> Username: jarauh  
> Created at: 2020-11-05 07:36:13 UTC  
> Updated at: 2020-11-11 18:09:32 UTC  
> Closed at: 2020-11-11 18:09:32 UTC  
> Url: https://github.com/boostorg/math/issues/451  

The following code:  
```cpp  
#include <boost/math/distributions/poisson.hpp>  
int main () {  
  double eps = std::numeric_limits<double>::epsilon();  
  boost::math::poisson_distribution<double> dist(2000 * 100 * eps);  
  boost::math::cdf(dist, 1754)  
  return 0;  
}  
```  
fails with  
> terminate called after throwing an instance of 'boost::wrapexcept<std::overflow_error>'  
>  what():  Error in function boost::math::tgamma<long double>(long double): Result of tgamma is too large to represent.  
  
The cdf basically calls `gamma_q`, so the following code (in which I have replaced eps by 2.22045e-16) gives the same error:  
```cpp  
#include <boost/math/special_functions/gamma.hpp>  
int main () {  
  std::cout << boost::math::gamma_q(1755, 4.4e-11 /* 2000 * 100 * eps */ ) << "\n";  
  return 0;  
}  
```  
  
I briefly looked into the code of `gamma_q`, and it seems that quite a few regions of the parameter space receive special treatment.  Apparently, I stumbled upon a region that has so far been overlooked.

---

## Comment 1

> Username: jzmaddock  
> Created at: 2020-11-05 12:06:19 UTC  
> Url: https://github.com/boostorg/math/issues/451#issuecomment-722336903  

Confirmed.  I'll have a fix shortly.

---

## Comment 2

> Username: jzmaddock  
> Created at: 2020-11-05 19:44:51 UTC  
> Url: https://github.com/boostorg/math/issues/451#issuecomment-722603043  

A fix is in the PR referenced above.

---

## Comment 3

> Username: jarauh  
> Created at: 2020-11-06 12:59:28 UTC  
> Url: https://github.com/boostorg/math/issues/451#issuecomment-723068002  

Thanks for the quick fix!
