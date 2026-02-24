# #747 - cpp_double_fp documentation error [Closed]

> Username: jrade  
> Created at: 2025-12-28 15:11:16 UTC  
> Updated at: 2025-12-28 19:41:20 UTC  
> Closed at: 2025-12-28 19:41:20 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/747  

The page  
https://www.boost.org/doc/libs/latest/libs/multiprecision/doc/html/boost_multiprecision/tut/floats/cpp_double_fp_backend.html  
states that one should include the header  
`boost/multiprecision/cpp_double_fp_backend.hpp`  
This is wrong. There is no such header. The correct header is  
`boost/multiprecision/cpp_double_fp.hpp`

---

## Comment 1

> Username: ckormanyos  
> Created at: 2025-12-28 15:40:41 UTC  
> Updated at: 2025-12-28 15:41:38 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/747#issuecomment-3694835775  

Thank you @jrade I will fix this. We just published 1.90 and that release is closed. So the correction won't actually make it into Boost until spring for Boost 1.91.

---

## Comment 2

> Username: ckormanyos  
> Created at: 2025-12-28 15:49:26 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/747#issuecomment-3694841740  

See also #748

---

## Comment 3

> Username: ckormanyos  
> Created at: 2025-12-28 16:05:01 UTC  
> Updated at: 2025-12-28 16:14:55 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/747#issuecomment-3694852369  

Test locally with the following.  
  
```  
#include <boost/math/constants/constants.hpp>  
#include <boost/multiprecision/cpp_double_fp.hpp>  
  
#include <iomanip>  
#include <iostream>  
#include <limits>  
#include <sstream>  
  
auto main() -> int;  
  
auto main() -> int  
{  
  using double_fp_type = boost::multiprecision::cpp_double_double;  
  
  const double_fp_type sqr { sqrt(boost::math::constants::pi<double_fp_type>()) };  
  
  std::stringstream strm { };  
  
  strm << std::setprecision(std::numeric_limits<double_fp_type>::digits10) << std::fixed << sqr;  
  
  std::cout << strm.str() << std::endl;  
}  
```

---

## Comment 4

> Username: ckormanyos  
> Created at: 2025-12-28 19:41:20 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/747#issuecomment-3694992864  

Fixed by #748
