# #675 - Swtich to BOOST_MATH_HEADER_DEPRECATED breaks Gentoo build [Closed]

> Username: mborland  
> Created at: 2021-08-22 08:30:16 UTC  
> Updated at: 2021-08-24 06:01:12 UTC  
> Closed at: 2021-08-24 06:01:12 UTC  
> Url: https://github.com/boostorg/math/issues/675  

As reported by @SoapGentoo [here](https://github.com/boostorg/math/commit/e8c7086afca6252979b38e8dedf1425e95400d39).

---

## Comment 1

> Username: SoapGentoo  
> Created at: 2021-08-22 09:23:04 UTC  
> Url: https://github.com/boostorg/math/issues/675#issuecomment-903239512  

On boost 1.77  
```  
#include <boost/math/common_factor_ct.hpp>  
  
int main() {  
	return 0;  
}  
```  
leads to  
```  
/usr/include/boost/math/common_factor_ct.hpp:17:68: error: _Pragma takes a parenthesized string literal  
   17 | BOOST_MATH_HEADER_DEPRECATED("<boost/integer/common_factor_ct.hpp>");  
      |                                                                    ^  
In file included from /usr/include/boost/math/common_factor_ct.hpp:15,  
                 from test.cpp:1:  
/usr/include/boost/math/common_factor_ct.hpp:17:1: error: ‘_Pragma’ does not name a type  
   17 | BOOST_MATH_HEADER_DEPRECATED("<boost/integer/common_factor_ct.hpp>");  
      | ^~~~~~~~~~~~~~~~~~~~~~~~~~~~  
```  
the synthesis of the warning pragma is wrong in `header_deprecated.hpp`. Restoring this to `#include <boost/config/header_deprecated.hpp>` and `BOOST_HEADER_DEPRECATED` fixes this.

---

## Comment 2

> Username: mborland  
> Created at: 2021-08-22 09:26:09 UTC  
> Url: https://github.com/boostorg/math/issues/675#issuecomment-903239830  

I will have a fix up shortly along those lines. The issue with just reverting is that it introduces a dependency that then breaks standalone mode.
