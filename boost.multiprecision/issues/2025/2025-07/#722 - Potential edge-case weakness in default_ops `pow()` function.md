# #722 - Potential edge-case weakness in default_ops `pow()` function [Closed]

> Username: ckormanyos  
> Created at: 2025-07-12 08:50:27 UTC  
> Updated at: 2025-07-12 18:34:54 UTC  
> Closed at: 2025-07-12 18:34:54 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/722  

There might be a potential weakness in an uncommon edge-case of the `pow()` function.  
  
It involves the result of $-{\infty}^{NaN}$.  
  
I believe the standard claims that ${\pm}{\infty}^{NaN}=NaN$, but the multiprecision types (I tried `cpp_dec_float` and `cpp_bin_float`) get an infinite result for $-{\infty}^{NaN}$.  
  
The offending code is below. If the edge-case weakness gets confirmed, I can handle the fix in `default_ops.hpp`.  
  
Cc: @jzmaddock and @mborland   
  
```  
#include <boost/multiprecision/cpp_bin_float.hpp>  
#include <boost/multiprecision/cpp_dec_float.hpp>  
  
#include <cmath>  
#include <iomanip>  
#include <iostream>  
#include <limits>  
#include <typeinfo>  
  
template<typename FloatType>  
auto test_more_pow_edges() -> void  
{  
  using float_type = FloatType;  
  
  std::cout << "Testing type: " << typeid(float_type).name() << std::endl;  
  
  using boost::multiprecision::isnan;  
  using std::isnan;  
  using std::pow;  
  
  {  
    float_type dbl_pow = pow(+std::numeric_limits<float_type>::infinity(), std::numeric_limits<float_type>::quiet_NaN());  
  
    std::stringstream strm { };  
  
    strm << "result +inf^NaN: " << dbl_pow << ", isnan? " << std::boolalpha << (isnan)(dbl_pow);  
  
    std::cout << strm.str() << std::endl;  
  }  
  
  {  
    float_type dbl_pow = pow(-std::numeric_limits<float_type>::infinity(), std::numeric_limits<float_type>::quiet_NaN());  
  
    std::stringstream strm { };  
  
    strm << "result -inf^NaN: " << dbl_pow << ", isnan? " << std::boolalpha << (isnan)(dbl_pow);  
  
    std::cout << strm.str() << '\n' << std::endl;  
  }  
}  
  
auto main() -> int  
{  
  test_more_pow_edges<double>();   
  
  {  
    using mp_backend_type = boost::multiprecision::cpp_bin_float<50>;  
    using mp_type = boost::multiprecision::number<mp_backend_type, boost::multiprecision::et_off>;  
  
    test_more_pow_edges<mp_type>();  
  }  
  
  {  
    using mp_backend_type = boost::multiprecision::cpp_dec_float<50>;  
    using mp_type = boost::multiprecision::number<mp_backend_type, boost::multiprecision::et_off>;  
  
    test_more_pow_edges<mp_type>();  
  }  
}  
```

---

## Comment 1

> Username: ckormanyos  
> Created at: 2025-07-12 18:34:54 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/722#issuecomment-3065955342  

Fixed by b5725b5ba8951734d51caf0f430c8fcb93b950a6
