# #383 - Incorrect results with gauss_kronrod when used with mpfr_float [Closed]

> Username: duhadler  
> Created at: 2020-06-28 18:38:19 UTC  
> Updated at: 2020-07-31 17:55:49 UTC  
> Closed at: 2020-07-31 17:55:49 UTC  
> Url: https://github.com/boostorg/math/issues/383  

Boost Math still contains a lot of code which assumes that ```std::numeric_limits``` is fully available for all data types of interest. Unfortunatly this is not true for ```mpfr_float```.  
  
As an example, consider the following code, using the integration routine ```gauss_kronrod```:  
  
  
```  
mpfr_float my_func_gauss_kronrod_mpfr(mpfr_float t) { return exp(-t * t / 2); }  
  
void demo_scalar_mpfr_calc_gauss_kronrod()  
{  
	int dps = 30;  
	mpfr_float::default_precision(dps);  
	std::cout << "dps: " << mpfr_float::default_precision() << std::endl;  
	std::cout << std::setprecision(dps);  
  
	gauss_kronrod<mpfr_float, 21> integrator;  
	mpfr_float termination = sqrt(std::numeric_limits<mpfr_float>::epsilon());  
	mpfr_float error;  
	mpfr_float L1;  
	mpfr_float a = 0;  
	mpfr_float b = std::numeric_limits<mpfr_float>::infinity();  
	unsigned max_depth = 10;  
	mpfr_float tol = sqrt(std::numeric_limits<mpfr_float>::epsilon());  
	mpfr_float Q = integrator.integrate(my_func_gauss_kronrod_mpfr, a, b, max_depth, tol, &error, &L1);  
	std::cout << "Integral1: = " << Q << std::endl;  
	using boost::math::constants::pi;  
	mpfr_float result = 0.5 * sqrt(2 * pi<mpfr_float>());  
	std::cout << "Integral2: = " << result << std::endl;  
}  
```  
  
Compiling this code leads to the following warning (output from VS 2019):  
  
```  
1>I:\Desktop\ExternalLibraries\Boost\boost/math/special_functions/legendre_stieltjes.hpp(217): warning C4307: '*': integral constant overflow  
1>I:\Desktop\ExternalLibraries\Boost\boost/math/special_functions/legendre_stieltjes.hpp(155): note: while compiling class template member function 'std::vector<Real,std::allocator<_Ty>> boost::math::legendre_stieltjes<Real>::zeros(void) const'  
1>        with  
1>        [  
1>            Real=boost::multiprecision::mpfr_float,  
1>            _Ty=boost::multiprecision::mpfr_float  
1>        ]  etc etc  
```  
  
While this just a warning, the calculated result is highly inaccurate:  
  
```  
dps: 30  
Integral1: = 1.24896845813744320321034174324  
Integral2: = 1.25331413731550025120788264241  
```  
  
This can be fixed, in the following very clumsy way, which defies the purpose of templates, but emphasizes that, since for ```mpfr_float```, ```std::numeric_limits<Real>::digits10``` returns the maximal precision, and not the current precision, ```digits10``` needs to be avoided specifically for ```mpfr_float``` to get a meaningful result:  
  
In the file ```\boost\math\special_functions\legendre_stieltjes.hpp```, change the lines 215-218:  
  
```  
            const Real x_nk = boost::math::tools::newton_raphson_iterate(f, x_nk_guess,  
                p.first, p.second,  
                2 * std::numeric_limits<Real>::digits10,  
                number_of_iterations);  
```  
  
to  
  
```  
            int digits = 0;  
            if (std::is_same<Real, mpfr_float>::value) { digits = mpfr_float::default_precision();}  
            else { digits = std::numeric_limits<Real>::digits10; }  
            digits = digits * 2;  
  
            const Real x_nk = boost::math::tools::newton_raphson_iterate(f, x_nk_guess,  
                p.first, p.second,  
                digits,  
                number_of_iterations);  
```  
  
This will preserve the old behaviour for all other data types, and provide the intended value of ```digits``` for ```mpfr_float```. The result is now:  
  
```  
dps: 30  
Integral1: = 1.25331413731550025120788264348  
Integral2: = 1.25331413731550025120788264241  
```  
  
  
  
  
Finally, a very minor thing:  
  
I suggest to add the following lines in the file ```\boost\math\specialfunctions.hpp```:  
  
#include <boost/math/special_functions/gegenbauer.hpp>   
#include <boost/math/special_functions/jacobi.hpp>   
#include <boost/math/special_functions/legendre_stieltjes.hpp>   
  
In this way, all headers related to special functions are available in one file.

---

## Comment 1

> Username: NAThompson  
> Created at: 2020-06-28 19:10:44 UTC  
> Url: https://github.com/boostorg/math/issues/383#issuecomment-650808769  

@duhadler : Yup, nice catch. Note that this is basically the same issue as [this](https://github.com/boostorg/multiprecision/issues/245).  
  
You're right that there's a lot of code that makes this assumption; so be prepared to hit more bugs when using MPFR.

---

## Comment 2

> Username: jzmaddock  
> Created at: 2020-07-27 18:57:35 UTC  
> Url: https://github.com/boostorg/math/issues/383#issuecomment-664578187  

The referenced PR should fix this: the use of digits10 in this case wasn't quite right anyway as newton_raphson_iterate take bits precision, not decimal places.
