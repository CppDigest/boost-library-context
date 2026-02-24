# #1294 - Double promotion for the logistic quantile doesn't work? [Closed]

> Username: WarrenWeckesser  
> Created at: 2025-08-08 17:17:06 UTC  
> Updated at: 2025-08-14 12:09:02 UTC  
> Closed at: 2025-08-14 12:09:02 UTC  
> Url: https://github.com/boostorg/math/issues/1294  

I'm looking into improving the precision of the logistic quantile function, and along the way I've found that I can't seem to get double promotion to work with that function.  Here's my test code:  
  
```  
#include <iostream>  
#include <boost/math/distributions/logistic.hpp>  
  
using namespace std;  
using namespace boost::math::policies;  
using boost::math::logistic_distribution;  
  
typedef policy<  
    promote_float<false>,  
    promote_double<false>  
> no_promotion;  
  
typedef policy<  
    promote_float<true>,  
    promote_double<true>  
> with_promotion;  
  
int main()  
{  
    logistic_distribution<double, no_promotion> dist;  
    logistic_distribution<double, with_promotion> dist_promote;  
    logistic_distribution<long double, no_promotion> dist_longdouble;  
    double p = 2049.0/4096;  
    double ref = 0.0009765625776102258;  // Verified with both Wolfram Alpha and mpmath  
    double x = quantile(dist, p);  
    double xpromote = quantile(dist_promote,  p);  
    double xld = static_cast<double>(quantile(dist_longdouble, static_cast<long double>(p)));  
    cout << fixed << setw(25) << setprecision(17) << p << " p" << endl;  
    cout << scientific << setw(25) << setprecision(17) << x << " double"<< endl;  
    cout << scientific << setw(25) << setprecision(17) << xpromote << " double with promotion" << endl;  
    cout << scientific << setw(25) << setprecision(17) << xld << " long double"<< endl;  
    cout << scientific << setw(25) << setprecision(17) << ref << " reference"<< endl;  
  
    return 0;  
}  
```  
  
I'm on a Linux machine using gcc, where `long double` is extended (i.e. 80 bit) precision.  
  
Output:  
```  
      0.50024414062500000 p  
  9.76562577610170027e-04 double  
  9.76562577610170027e-04 double with promotion  
  9.76562577610225755e-04 long double  
  9.76562577610225755e-04 reference  
```  
  
For any value of `p` that I use, the values `x` (computed with no promotion) and `xpromote` (computed with promotion) are the same.  The value computed with the distribution explicitly declared `long double` is correct (checked with Wolfram Alpha and `mpmath`).

---

## Comment 1

> Username: mborland  
> Created at: 2025-08-08 17:29:55 UTC  
> Url: https://github.com/boostorg/math/issues/1294#issuecomment-3168806879  

It looks like all the computations are being done with STL functions rather than `boost::math::` functions where the policy is passed along with the parameters. I think we'd need to do some `std::conditional` logic to figure out what `RealType` should be promoted to based of the policy, and then cast all the arguments into STL functions.
