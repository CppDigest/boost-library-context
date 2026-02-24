# #588 - boost::multiprecision::pow doesn't support cpp_int exponent [Closed]

> Username: radj307  
> Created at: 2024-01-22 21:35:08 UTC  
> Updated at: 2024-01-23 10:56:46 UTC  
> Closed at: 2024-01-23 10:56:45 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/588  

The `boost::multiprecision::pow` function is missing an overload for using `cpp_int` as the base and exponent.    
Tested on MSVC 19.34.31947 & gcc 10 with boostmp 1.84 standalone.  
  
```cpp  
#include <boost/multiprecision/cpp_int.hpp>  
#include <boost/multiprecision/cpp_dec_float.hpp>  
  
namespace mp = boost::multiprecision;  
  
int main()  
{  
    using mp::cpp_int;  
    using cpp_float = mp::cpp_dec_float_100;  
  
    mp::pow(cpp_float{ 2 }, cpp_float{ 2 }); //< works  
    mp::pow(cpp_int{ 2 }, cpp_int{ 2 }); //< ERROR: no matching function for call to ‘pow(boost::multiprecision::cpp_int, boost::multiprecision::cpp_int)’  
  
    return 0;  
}  
```

---

## Comment 1

> Username: ckormanyos  
> Created at: 2024-01-23 05:21:28 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/588#issuecomment-1905312056  

> The `boost::multiprecision::pow` function is missing an overload for using `cpp_int`  
  
Thanks @radj307 for this observation.  
  
There are several perspectives on this issue. Intuitively, it's not immediately clear to me if an overload for a non-integral type (in the sense of `std::is_integral`) even should or should not be supported?  
  
Cc: @jzmaddock

---

## Comment 2

> Username: jzmaddock  
> Created at: 2024-01-23 10:56:45 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/588#issuecomment-1905793075  

This was deliberately not supported: even relatively small integer exponents can blow the result up so large that you will run the machine out of memory, so yes, only native integers are supported as the exponent.  powm should be supported for all arguments a cpp_int though.
