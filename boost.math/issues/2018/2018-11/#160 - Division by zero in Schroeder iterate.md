# #160 - Division by zero in Schroeder iterate [Closed]

> Username: NAThompson  
> Created at: 2018-11-15 00:37:24 UTC  
> Updated at: 2018-11-30 19:55:29 UTC  
> Closed at: 2018-11-30 19:54:45 UTC  
> Url: https://github.com/boostorg/math/issues/160  

Code to reproduce:  
  
```cpp  
#include <iostream>  
#include <vector>  
#include <string>  
#include <boost/math/tools/polynomial.hpp>  
#include <boost/math/tools/roots.hpp>  
#include <boost/math/special_functions/binomial.hpp>  
  
using std::string;  
using boost::math::tools::polynomial;  
using boost::math::binomial_coefficient;  
using boost::math::tools::schroder_iterate;  
  
template<class Real>  
void daubechies_coefficients(size_t p)  
{  
    std::vector<Real> coeffs(p);  
    for (size_t k = 0; k < coeffs.size(); ++k) {  
      coeffs[k] = binomial_coefficient<Real>(p-1+k, k);  
    }  
  
    polynomial<Real> P(coeffs.begin(), coeffs.end());  
    polynomial<Real> P_prime = P.prime(); // Basically syntactic sugar from the PR  
    polynomial<Real> P_dprime = P_prime.prime();  
  
    auto f = [&](Real x)->std::tuple<Real, Real, Real>  
    {  
        return std::make_tuple<Real, Real, Real>(P(x), P_prime(x), P_dprime(x));  
    };  
  
    Real guess = 0;  
    Real minimum = -100000;  
    Real maximum = 100000;  
    boost::uintmax_t digits = std::numeric_limits<Real>::digits10;  
    boost::uintmax_t iterations = 15;  
    Real r = schroder_iterate(f, guess, minimum, maximum, digits, iterations);  
    std::cout << "Root: " << r << ", P(r) = " << P(r) << std::endl;  
}  
  
int main()  
{  
    daubechies_coefficients<double>(8);  
}  
```  
  
Compile with:  
  
```bash  
$ clang++ --std=gnu++17 -O0 -g -fsanitize=address -fsanitize=undefined -Wall -Wfatal-errors -march=native main.cpp  
```  
  
Error message:  
  
```bash  
include/boost/math/tools/roots.hpp:519:19: runtime error: division by zero  
SUMMARY: UndefinedBehaviorSanitizer: undefined-behavior include/boost/math/tools/roots.hpp:519:19  
```

---

## Comment 1

> Username: jzmaddock  
> Created at: 2018-11-30 19:55:29 UTC  
> Url: https://github.com/boostorg/math/issues/160#issuecomment-443320887  

Pending CI, this should be fixed now, as a side effect your example now uses one less iteration to find the root.
