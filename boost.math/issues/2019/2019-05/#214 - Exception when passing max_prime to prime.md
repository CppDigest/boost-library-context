# #214 - Exception when passing max_prime to prime [Closed]

> Username: jmdion  
> Created at: 2019-05-25 21:01:22 UTC  
> Updated at: 2019-05-26 10:45:50 UTC  
> Closed at: 2019-05-26 10:45:50 UTC  
> Url: https://github.com/boostorg/math/issues/214  

Code to reproduce:  
```  
#include <iostream>  
#include <boost/math/special_functions/prime.hpp>  
int main()    
{  
	try {  
		boost::math::prime(boost::math::max_prime);  
	}  
	catch (std::domain_error& e) {  
		std::cout << e.what() << std::endl;  
	}  
}  
```  
Maybe max_prime should be 9'999 instead of 10'000.

---

## Comment 1

> Username: jzmaddock  
> Created at: 2019-05-26 09:15:53 UTC  
> Url: https://github.com/boostorg/math/issues/214#issuecomment-495982876  

Confirmed.
