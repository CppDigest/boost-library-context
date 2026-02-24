# #495 - Error in square root for cpp_dec_float_100 [Open]

> Username: abeccaro  
> Created at: 2022-09-17 17:56:51 UTC  
> Updated at: 2022-09-20 09:48:59 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/495  

I recently updated boost from 1.71.0 to 1.80.0 and I started getting an error. After some investigation the problem arises from an incorrect rounding on the square root of 49 that makes it 7.000...01 instead of just 7. Here's the code that shows the issue  
  
```c++  
#include <boost/multiprecision/cpp_dec_float.hpp>  
#include <iostream>  
  
using boost::multiprecision::cpp_dec_float_100;  
using std::string;  
  
int main() {  
    cpp_dec_float_100 n(49);  
    n = sqrt(n);  
    if (n == (uint32_t) n)  
        std::cout << "This should be logged as sqrt(49) = 7" << std::endl;  
    else  
        std::cout << "Error: sqrt(49) != 7" << std::endl;  
  
    std::cout << std::endl << n.str() << std::endl;  
      
    return 0;  
}  
```  
  
```  
Error: sqrt(49) != 7  
  
7.000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000001  
```  
  
I also replicated the issue [here](https://wandbox.org/permlink/LdTnsrcG6DPbavgB) with version 1.79.0. In version 1.71.0 the calculation was correct.

---

## Comment 1

> Username: ckormanyos  
> Created at: 2022-09-20 09:45:13 UTC  
> Updated at: 2022-09-20 09:48:59 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/495#issuecomment-1252105640  

Thank you for your query Alex (@abeccaro)  
  
Sorry about the late response, I was absent for a few weeks without internet.  
  
> I recently updated boost from 1.71.0 to 1.80.0 and I started getting an error  
  
Your observation is correct, but I'm not sure if this is actually an error or simply an unfortunate consequence of the lack of exactness of the `cpp_dec_float` floating-point type.  
  
Please remember that this type is not an exact integral type, but rather a floating-point type. Due to this fact, the square root of 49 is not necessarily guaranteed to be the exact integral result 7, but rather a floating-point result that is very close to 7.  
  
If you look at the modified example below, then the result of `sqrt(n)` when printed with the precision of `cpp_dec_float_100` is _exact_ to within the precision of the type. The inexact digit(s) arise in the so-called _guard_ _digits_ of the type, and are considered inprecise.  
  
When testing the closeness of floating-point types, it may be better to use a close-fraction approach, as shown in the modified code. See the subroutine `is_close_fraction()` in the modified sample.  
  
So, why does this arise in Boost today when you recently switched the version? Well, we did, in fact, rework the multiplication routines of `cpp_dec_float` in recent versions. I can't remember the exact version of the rework, but it lies within the version range you mentioned in your post. This must have (inadvertently) led to this change. We do not at the moment consider this to be a breaking change. But we do apologize for the detail-change that leads to or lead to your code break.  
  
```cpp  
#include <iomanip>  
#include <iostream>  
  
#include <boost/multiprecision/cpp_dec_float.hpp>  
  
using boost::multiprecision::cpp_dec_float_100;  
using std::string;  
  
namespace detail  
{  
  template<typename FloatingPointType>  
  auto is_close_fraction(FloatingPointType a,  
                         FloatingPointType b,  
                         FloatingPointType tol = std::numeric_limits<FloatingPointType>::epsilon()) -> bool  
  {  
    using local_floating_point_type = FloatingPointType;  
  
    using std::fabs;  
  
    const auto delta = fabs(1 - fabs(a / b));  
  
    return (delta < tol);  
  }  
}  
  
int main()  
{  
  cpp_dec_float_100 n(49);  
  
  n = sqrt(n);  
  
  if(detail::is_close_fraction(n, cpp_dec_float_100(static_cast<std::uint32_t>(n))))  
  {  
    std::cout << "This should be logged as sqrt(49) = 7" << std::endl;  
  }  
  else  
  {  
    std::cout << "Error: sqrt(49) != 7" << std::endl;  
  }  
  
  std::cout << std::setprecision(std::numeric_limits<cpp_dec_float_100>::digits10)  
            << std::fixed  
            << n  
            << std::endl;  
}  
```
