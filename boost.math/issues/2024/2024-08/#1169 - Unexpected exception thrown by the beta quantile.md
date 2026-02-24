# #1169 - Unexpected exception thrown by the beta quantile [Closed]

> Username: WarrenWeckesser  
> Created at: 2024-08-04 02:49:16 UTC  
> Updated at: 2024-09-07 16:36:41 UTC  
> Closed at: 2024-09-07 16:36:41 UTC  
> Url: https://github.com/boostorg/math/issues/1169  

I'm looking into https://github.com/scipy/scipy/issues/21303.  
  
With the latest develop (ef3892c5fb29a667e5cd64a9f94a6a9e1ce39898), this code crashes:  
  
```  
#include <iostream>  
#include <boost/math/distributions/beta.hpp>  
  
using namespace std;  
using boost::math::beta_distribution;  
  
int main(int argc, char *argv[])  
{  
    double a = 5.0;  
    double b = 5.0;  
    double p = 0.5;  
   
    beta_distribution<> dist(a, b);  
    double x = quantile(dist, p);  
  
    cout << scientific << setw(23) << setprecision(16) << x << endl;  
  
    return 0;  
}  
```  
  
Compile and run on Linux:  
  
```  
$ g++ -std=c++17 -I/home/warren/repos/git/forks/boost/math/include beta_quantile_crash.cpp -o beta_quantile_crash  
$ ./beta_quantile_crash   
terminate called after throwing an instance of 'boost::math::evaluation_error'  
  what():  Error in function boost::math::tools::newton_raphson_iterate<long double>: There appears to be no root to be found in boost::math::tools::newton_raphson_iterate, perhaps we have a local minima near current best guess of 0.249999999999999999986  
Aborted (core dumped)  
$   
```  
Note the reference to `long double` in the error message.  This code *doesn't* crash if I add  
  
```  
#define BOOST_MATH_PROMOTE_DOUBLE_POLICY      false  
```  
  
before importing boost/math.  
  
Let me know if you need any additional information.

---

## Comment 1

> Username: jzmaddock  
> Created at: 2024-08-04 10:14:09 UTC  
> Url: https://github.com/boostorg/math/issues/1169#issuecomment-2267488583  

I'm still not able to reproduce here... however, I do note that this special case is right on the cusp of two methods for determining the initial guess, and that initial guess appears to be `0.5 +- random_small_value` in this special case.  Can you please build and run with BOOST_MATH_INSTRUMENT defined and report the output back?  Thanks!!

---

## Comment 2

> Username: WarrenWeckesser  
> Created at: 2024-08-04 13:10:03 UTC  
> Url: https://github.com/boostorg/math/issues/1169#issuecomment-2267538181  

Thanks for taking a look.  Here's the run with `BOOST_MATH_INSTRUMENT` defined:  
  
```  
$ g++ -std=c++17 -DBOOST_MATH_INSTRUMENT=1 -I/home/warren/repos/git/forks/boost/math/include beta_quantile_crash.cpp -o beta_quantile_crash  
$ ./beta_quantile_crash   
lgamma_imp called with e N5boost4math7lanczos12lanczos17m64E  
/home/warren/repos/git/forks/boost/math/include/boost/math/special_functions/erf.hpp:1223 result_type = e  
/home/warren/repos/git/forks/boost/math/include/boost/math/special_functions/erf.hpp:1224 value_type = e  
/home/warren/repos/git/forks/boost/math/include/boost/math/special_functions/erf.hpp:1225 precision_type = N5boost4math8policies7digits2ILi64EEE  
/home/warren/repos/git/forks/boost/math/include/boost/math/special_functions/erf.hpp:1234 tag_type = St17integral_constantIiLi64EE  
/home/warren/repos/git/forks/boost/math/include/boost/math/special_functions/erf.hpp:443 64-bit precision erf_imp called  
Newton_raphson_iterate, guess = 0.49999999999999999997289494568786239, min = 0, max = 0.49999999999999999997289494568786239, digits = 32, max_iter = 18446744073709551615  
Newton iteration 1, delta = 1, residual = -2.1684043449710088680149056017398834e-19  
Newton iteration 2, delta = -0.10788077716941784783843700373795471, residual = -0.28768207245178092760530541571739604  
terminate called after throwing an instance of 'boost::math::evaluation_error'  
  what():  Error in function boost::math::tools::newton_raphson_iterate<long double>: There appears to be no root to be found in boost::math::tools::newton_raphson_iterate, perhaps we have a local minima near current best guess of 0.249999999999999999986  
Aborted (core dumped)  
```

---

## Comment 3

> Username: jzmaddock  
> Created at: 2024-08-10 15:21:38 UTC  
> Url: https://github.com/boostorg/math/issues/1169#issuecomment-2282184908  

@WarrenWeckesser are you able to verify that the referenced PR fixes things at your end?  Thanks!

---

## Comment 4

> Username: WarrenWeckesser  
> Created at: 2024-08-10 17:19:30 UTC  
> Url: https://github.com/boostorg/math/issues/1169#issuecomment-2282215926  

@jzmaddock, yes, with https://github.com/boostorg/math/pull/1175/commits/443469c024f52f78f79ce13a599d880240077a7b (third commit in #1175), my C++ test program (updated to check both `float` and `double`, and to use a custom `policy` instead of the macro), handles that beta quantile edge case for both floating point types, with the promotion policies of `float` and `double` either true or false.
