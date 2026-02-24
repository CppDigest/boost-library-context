# #384 - cpp_rational hangs when using a particular expression [Closed]

> Username: sikefield3  
> Created at: 2021-10-19 14:00:38 UTC  
> Updated at: 2021-12-04 13:48:25 UTC  
> Closed at: 2021-12-04 13:48:25 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/384  

```  
#include <boost/multiprecision/cpp_int.hpp>  
#include <iostream>  
  
using namespace boost::multiprecision;  
  
int main(int argc, char **argv) {  
	std::cout << "Version of the C++-standard: " << __cplusplus << std::endl;	  
	std::cout << "Using Boost version: " << BOOST_VERSION << std::endl;  
            
	cpp_rational F ("9135403136690743/4590092002500000");  
	cpp_rational R ("9180184005000000/44780868309257");  
	cpp_rational U ("260339311693181935293341827902181238/1854867813993550353066284746121291951614531853469686663569245411260437");  
	  
	std::cout <<  "F = " << F << ", R = " << R << ", U = " << U << std::endl;  
	cpp_rational L = R - 2*F*U / (2-F) * (2-F+F*U);  
	std::cout <<  "L = " << L  << std::endl;  
	std::cout << "Done!" << std::endl;      
}  
```  
gives the following output:  
```  
Version of the C++-standard: 201402  
Using Boost version: 107700  
F = 9135403136690743/4590092002500000, R = 9180184005000000/44780868309257, U = 260339311693181935293341827902181238/1854867813993550353066284746121291951614531853469686663569245411260437  
```  
and then the computer hangs up with maximum CPU load.  
  
confirmed with Boost version 1.75 and 1.77, but doesn't occur in version 1.66  
if the number-strings  get smaller, or the expression for L gets simpler, the issue goes away  
  
kernel: Linux 5.3.18-59.27-default x86_64  
gcc (SUSE Linux) 7.5.0

---

## Comment 1

> Username: jzmaddock  
> Created at: 2021-12-04 13:48:25 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/384#issuecomment-986030083  

Duplicates https://github.com/boostorg/multiprecision/issues/370.  
  
Fixed in forthcoming 1.78 release due in a few days.
