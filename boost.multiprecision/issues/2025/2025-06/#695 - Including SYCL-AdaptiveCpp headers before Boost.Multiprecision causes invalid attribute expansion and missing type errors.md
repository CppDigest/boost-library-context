# #695 - Including SYCL/AdaptiveCpp headers before Boost.Multiprecision causes invalid attribute expansion and missing type errors [Closed]

> Username: Monstermel  
> Created at: 2025-06-10 03:03:40 UTC  
> Updated at: 2025-06-11 13:23:59 UTC  
> Closed at: 2025-06-11 13:23:59 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/695  

### Environment  
- Boost version: 1.88.0  
- AdaptiveCpp (hipSYCL) version: 25.02.0  
- C++ compiler: (e.g. clang++ 17)  
- Platform: (e.g. Linux x86_64)  
  
### Problem Summary  
Including SYCL headers (AdaptiveCpp/hipSYCL) before Boost.Multiprecision headers such as `boost/multiprecision/cpp_int.hpp` leads to compilation errors due to macro expansion that places C++ attributes in invalid locations, and also references a missing type in Boost.Math.  
  
### Minimal Reproducer  
```cpp  
#include "sycl/sycl.hpp"  
#include "boost/multiprecision/cpp_int.hpp"  
  
int main() {  
  return 0;  
}  
```  
This fails to compile. If the include order is reversed (Boost first, then SYCL), it compiles successfully.  
  
### Full Error Output (excerpt)  
```  
/usr/local/include/boost/math/special_functions/fpclassify.hpp:89:19: error: an attribute list cannot appear here  
   89 | template<> inline BOOST_MATH_GPU_ENABLED bool (isnan)(float x) { return x != x; }  
      |                   ^~~~~~~~~~~~~~~~~~~~~~  
...   
/usr/local/include/boost/multiprecision/detail/functions/pow.hpp:789:38: error: no type named 'rounding_error' in namespace 'boost::math'  
  789 |    BOOST_MP_CATCH(const boost::math::rounding_error&)  
      |                         ~~~~~~~~~~~~~^  
```  
  
### Workaround  
- Including Boost headers before SYCL headers avoids macro expansion issues. However, this is only a workaround and not a proper fix, as include order should not affect compilation.  
  
### Expected  
- Boost and AdaptiveCpp should be compatible regardless of include order.  
- Macro expansion should not result in invalid attribute placement.
