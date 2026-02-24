# #322 - Failed assertion in Lehmer GCD [Closed]

> Username: sansc  
> Created at: 2021-05-09 00:30:37 UTC  
> Updated at: 2021-06-14 10:10:36 UTC  
> Closed at: 2021-06-14 10:10:36 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/322  

This code:  
```  
#include <iostream>  
#include <numeric>  
#include <boost/integer/common_factor_ct.hpp>  
#include <boost/multiprecision/cpp_int.hpp>   
  
namespace mp = boost::multiprecision;  
  
int main() {  
	mp::cpp_int i ("2722258935367507707706996859454145691647");  
	mp::cpp_int j ("1144561273430837494885949696426");  
	std::cout << i << "\n" << j << std::endl;  
	auto gcd = boost::math::gcd(i, j);  
	std::cout << gcd << "\n";  
}  
```  
  
Fails at compile time with error message.  
  
`Assertion failed: u > v, file /boost/multiprecision/cpp_int/misc.hpp, line 811`

---

## Comment 1

> Username: jzmaddock  
> Created at: 2021-05-09 07:58:32 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/322#issuecomment-835734432  

Strangely, I can reproduce with gcc but not msvc: will investigate.
