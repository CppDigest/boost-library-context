# #893 - Assertion fail in tanh_sinh when using cpp_dec_float_100 [Closed]

> Username: mjziebarth  
> Created at: 2022-12-05 17:09:57 UTC  
> Updated at: 2023-01-15 12:40:36 UTC  
> Closed at: 2023-01-15 12:40:36 UTC  
> Url: https://github.com/boostorg/math/issues/893  

Hi all,  
  
this might be closely related to #155. I'm not sure about your issue policy, so feel free  
to close this one and reopen the old one.  
  
I have encountered a similar issue, triggering the ``Assertion `(left_min_complement * diff + a) > a' failed`` error  
when computing an integral using a higher precision boost multiprecision number type. After some investigation,  
I realized that the error occurs before the first call of the integrand, and I could boil down the problem to the  
integration bounds. When choosing an integration bound slightly lower than 1, the error occurs.  
  
I found out that the error can be relaxed by either choosing 1 as an integration bound and when switching to  
a native floating point type. Here's a minimal example that, on my machine, fails when compiled without further  
defines and succeeds with `NO_BUG` or `ALSO_NO_BUG` defined.  
  
  
```C++  
#include <boost/math/quadrature/tanh_sinh.hpp>  
#include <boost/multiprecision/cpp_dec_float.hpp>  
  
using boost::math::quadrature::tanh_sinh;  
  
#include <iostream>  
  
int main(){  
#ifdef ALSO_NO_BUG  
	typedef long double real;  
#else  
	typedef boost::multiprecision::cpp_dec_float_100 real;  
#endif  
  
	auto fun = [](real x) -> real {  
		return 1.0;  
	};  
  
	tanh_sinh<real> integrator;  
	const real a = 0.0;  
#ifdef NO_BUG  
	const real b = 1.0;  
#else  
	const real b = 0.9999995515592481132478776023609116290187750667053638330158486516399489191171270344610533516275817076;  
#endif  
	real y = integrator.integrate(fun, a, b);  
	std::cout << std::setprecision(10) << y << "\n";  
  
	return 0;  
}  
```  
  
In case it's relevant, I am running an Arch linux system with boost 1.80.0-3 installed.
