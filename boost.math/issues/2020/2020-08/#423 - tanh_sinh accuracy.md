# #423 - tanh_sinh accuracy [Closed]

> Username: jzmaddock  
> Created at: 2020-08-21 10:19:33 UTC  
> Updated at: 2020-08-21 13:09:23 UTC  
> Closed at: 2020-08-21 11:39:11 UTC  
> Url: https://github.com/boostorg/math/issues/423  

@NAThompson : Trying to help a user on the boost-users mailing list and came up with this test case:  
  
```  
#include <boost/math/quadrature/tanh_sinh.hpp>  
  
int main()  
{  
   using BF = double;  
  
   boost::math::quadrature::tanh_sinh<BF> integrator;  
  
   BF tol = boost::math::tools::epsilon<BF>();  
   BF err, L1;  
   std::size_t levels;  
  
   std::cout << std::left << std::setw(25) << "Tolerance:" << tol << std::endl;  
  
   auto f1 = [](BF x) { return x / BF(sqrt(x * x - 0.25)); };  
   BF Q1 = integrator.integrate(f1, BF(0.5), BF(sqrt(BF(1.25))), tol, &err, &L1, &levels);  
  
   std::cout << std::setw(25) << "Error found:" << boost::math::relative_difference(BF(1), Q1) << std::endl;  
   std::cout << std::setw(25) << "Error estimate:" << err << std::endl;  
   std::cout << std::setw(25) << "L1 sum:" << L1 << std::endl;  
   std::cout << std::setw(25) << "Integral calculated:" << Q1 << std::endl;  
   std::cout << std::setw(25) << "Levels Used:" << levels << std::endl;  
  
   auto f2 = [](BF x) { return x / BF(sqrt(x * x - BF(0.25))) - x; };  
   BF Q2 = integrator.integrate(f2, BF(0.5), BF(sqrt(BF(1.25))));  
  
   std::cout << std::setw(25) << "Error found:" << boost::math::relative_difference(BF(0.5), Q2) << std::endl;  
   std::cout << std::setw(25) << "Error estimate:" << err << std::endl;  
   std::cout << std::setw(25) << "L1 sum:" << L1 << std::endl;  
   std::cout << std::setw(25) << "Integral calculated:" << Q2 << std::endl;  
   std::cout << std::setw(25) << "Levels Used:" << levels << std::endl;  
  
   auto f3 = [](BF x) { return (x - x * BF(sqrt(x * x - BF(0.25)))) / BF(sqrt(x * x - BF(0.25))); };  
   BF Q3 = integrator.integrate(f3, BF(0.5), BF(sqrt(BF(1.25))));  
  
   std::cout << std::setw(25) << "Error found:" << boost::math::relative_difference(BF(0.5), Q3) << std::endl;  
   std::cout << std::setw(25) << "Error estimate:" << err << std::endl;  
   std::cout << std::setw(25) << "L1 sum:" << L1 << std::endl;  
   std::cout << std::setw(25) << "Integral calculated:" << Q3 << std::endl;  
   std::cout << std::setw(25) << "Levels Used:" << levels << std::endl;  
  
}  
```  
  
The original test case used multiprecision types, but double works just as well... no matter what value I set `tol` to, the error always seems to be ~ sqrt(epsilon).  Any ideas?  
  
The output I see is:  
  
```  
Tolerance:               2.22045e-16  
Error found:             1.09228e-08  
Error estimate:          5.44654e-09  
L1 sum:                  1  
Integral calculated:     1  
Levels Used:             6  
Error found:             1.17448e-08  
Error estimate:          5.44654e-09  
L1 sum:                  1  
Integral calculated:     0.5  
Levels Used:             6  
Error found:             1.17448e-08  
Error estimate:          5.44654e-09  
L1 sum:                  1  
Integral calculated:     0.5  
Levels Used:             6  
```  
  
Note that the code requires this fix: https://github.com/boostorg/math/commit/d7ab3a9873cf88cbada681a57443788d31ebb3a1 otherwise asserts get triggered :(

---

## Comment 1

> Username: jzmaddock  
> Created at: 2020-08-21 11:39:11 UTC  
> Url: https://github.com/boostorg/math/issues/423#issuecomment-678247169  

I see the issue: the integral returned is always slightly too low, and matches the "missing" area between `[0.5, nextfloat(0.5)]`.  In other words there's a large-ish area right next to the singularity that we are unable to integrate because for any given type T there are no abscissa values that T can represent within the "missing area".

---

## Comment 2

> Username: NAThompson  
> Created at: 2020-08-21 13:09:23 UTC  
> Url: https://github.com/boostorg/math/issues/423#issuecomment-678281826  

oh boy. This reminds me of Kahan's spy function: There's always a function your quadrature routine can't integrate successfully.  
  
Thanks for fixing that bug; I'm amazed at the subtlety of tanh-sinh; it seems it's been well-used, yet nonetheless we're still pulling bugs out of it.
