# #799 - binomial distribution silently underflows when divide-by-zero occurs in ibeta_power_terms [Closed]

> Username: mckib2  
> Created at: 2022-07-12 16:20:02 UTC  
> Updated at: 2022-07-16 19:18:19 UTC  
> Closed at: 2022-07-16 08:33:26 UTC  
> Url: https://github.com/boostorg/math/issues/799  

As reported in https://github.com/scipy/scipy/issues/15101, there is a potential divide-by-zero in [ibeta_power_terms L392-L393](https://github.com/boostorg/math/blob/db2a7cbb44a84c4c9cba62232c07f18b22bd965d/include/boost/math/special_functions/beta.hpp#L392-L393) for some values of n, p, and k, e.g., n=1000, p=0.01, k=996.  This may not be the only instance of a possible divide-by-zero for these parameter choices.  
  
Suggested patch would be to avoid the logarithm calculation on line 393 if `p1 == 0`.  We are also curious why this underflow is not reported according to boost::math policy or why we can't reproduce any observable issue when run as a simple C++ application (GCC 9), e.g.:  
  
```cpp  
#include <iostream>  
#include "boost/math/distributions/binomial.hpp"  
  
int main() {  
  constexpr int n = 1000;  
  constexpr double p = .01;  
  constexpr int k = 996;  
  boost::math::binomial_distribution<double> b(n, p);  
  std::cout << std::setprecision(16) << "ans is " << boost::math::pdf(b, k) << std::endl;  
  return 0;  
}  
  
```

---

## Comment 1

> Username: fangzh-umich  
> Created at: 2022-07-13 14:24:05 UTC  
> Updated at: 2022-07-13 17:33:50 UTC  
> Url: https://github.com/boostorg/math/issues/799#issuecomment-1183292450  

As for reproducing in a simple C++ application, there are two things. First, `boost::math::pdf` with a `binomial_distribution` by default promotes `double` to `long double`, and `float` to `double`. Second, the divide-by-zero warning is caused by a floating point exception, which we have to check manually in C++.  
  
So to reproduce:  
```cpp  
#include <iostream>  
#include "boost/math/distributions/binomial.hpp"  
#include <cfenv>  
  
#pragma STDC FENV_ACCESS ON  
  
using namespace boost::math::policies;  
  
int main() {  
  constexpr int n = 1000;  
  constexpr double p = .01;  
  constexpr int k = 996;  
  boost::math::binomial_distribution<double, policy<promote_double<false> > > b(n, p);  
  std::cout << std::setprecision(16) << "ans is " << boost::math::pdf(b, k) << std::endl;  
  if(std::fetestexcept(FE_DIVBYZERO)) {  
    std::cout << "division by zero reported\n";  
  } else {  
    std::cout << "divsion by zero not reported\n";  
  }  
  return 0;  
}  
```  
This should output `division by zero reported`.  
  
Edit: Used an explicit policy not to promote `double`, instead of using `float` as the argument type.

---

## Comment 2

> Username: mckib2  
> Created at: 2022-07-14 16:06:44 UTC  
> Url: https://github.com/boostorg/math/issues/799#issuecomment-1184626168  

@fangzh-umich Thanks for clarifying that.  Do you think it's enough to check if `p1 == 0` or are there other changes you can see that would need to be made?  If it's that easy, we may be able to quickly patch it downstream

---

## Comment 3

> Username: jzmaddock  
> Created at: 2022-07-14 16:47:14 UTC  
> Url: https://github.com/boostorg/math/issues/799#issuecomment-1184665874  

If it makes any difference, this is next on my hit list of bugs to squash.  It will be too late for Boost-1.80 now though :(

---

## Comment 4

> Username: mckib2  
> Created at: 2022-07-14 16:56:02 UTC  
> Url: https://github.com/boostorg/math/issues/799#issuecomment-1184673742  

> If it makes any difference, this is next on my hit list of bugs to squash. It will be too late for Boost-1.80 now though :(  
  
No worries -- we have an easy workaround for now, but want to help out with getting an upstream fix for everyone else as well

---

## Comment 5

> Username: jzmaddock  
> Created at: 2022-07-15 18:20:48 UTC  
> Url: https://github.com/boostorg/math/issues/799#issuecomment-1185789810  

@mckib2 Can you see if the PR referenced above is sufficient?  
  
Note that your test case does not (and can not?) trigger a divide by zero:  the only division is in line 392:  
  
```  
            T p1 = pow(b1, a / b);  
```  
  
And b is always > 0 as checked at the start of ibeta_derivative when it verifies its parameters.  It would be nonsensical in any case, as b <= 0 would require k > n in the distribution.  
  
Unless I'm missing something?

---

## Comment 6

> Username: mckib2  
> Created at: 2022-07-15 18:27:00 UTC  
> Updated at: 2022-07-15 18:37:15 UTC  
> Url: https://github.com/boostorg/math/issues/799#issuecomment-1185794265  

The issue is a divide by zero reported when computing log(0) as detailed in this comment: https://github.com/scipy/scipy/issues/15101#issuecomment-1181889203

---

## Comment 7

> Username: mckib2  
> Created at: 2022-07-15 18:30:35 UTC  
> Url: https://github.com/boostorg/math/issues/799#issuecomment-1185799860  

> Can you see if the PR referenced above is sufficient?  
  
By looking at it, I don't believe this will resolve it as the divide-by-zero bit will still be flipped, but I'll be able to try this patch in situ later today

---

## Comment 8

> Username: jzmaddock  
> Created at: 2022-07-15 18:37:00 UTC  
> Url: https://github.com/boostorg/math/issues/799#issuecomment-1185807284  

Ah, my bad, just pushed a fix for that to the PR.  
  
Sorry I was looking for a literal divide by zero, not a synthesised one inside std::log.

---

## Comment 9

> Username: mckib2  
> Created at: 2022-07-15 18:42:06 UTC  
> Url: https://github.com/boostorg/math/issues/799#issuecomment-1185810718  

The changes look great now, thanks @jzmaddock! I will still pull and test later today. Don't feel you need to wait on me to merge if CI is green and the above snippet from @fangzh-umich runs clean

---

## Comment 10

> Username: mckib2  
> Created at: 2022-07-16 16:37:47 UTC  
> Url: https://github.com/boostorg/math/issues/799#issuecomment-1186234650  

@jzmaddock  FYI -- just tested your patch, works perfectly!  Many thanks

---

## Comment 11

> Username: jzmaddock  
> Created at: 2022-07-16 19:18:19 UTC  
> Url: https://github.com/boostorg/math/issues/799#issuecomment-1186270609  

Thanks for the confirmation.
