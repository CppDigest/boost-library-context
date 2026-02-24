# #143 - Consider enabling CTAD for the safe type [Open]

> Username: correaa  
> Created at: 2025-10-03 20:48:44 UTC  
> Updated at: 2025-10-03 20:53:33 UTC  
> Url: https://github.com/boostorg/safe_numerics/issues/143  

CTAD: class template argument deduction  
  
Such thing will allow this use case, in C++20 and higher.  
  
```cpp  
#include <boost/safe_numerics/safe_integer.hpp>  
  
void uncontrolled(double*, int);  // true external function  
  
using boost::safe_numerics::safe;  
  
int main() {  
  
    std::vector<double> v = {1.0, 2.0, 3.0};  
  
    uncontrolled(v.data(), safe<int>{v.max_size()});  // ok, throws  
  
//  uncontrolled(v.data(), safe{v.max_size()});  // needs CTAD  
}  
```  
  
complete code: https://godbolt.org/z/s8bYz9rqb  
  
The most basic code for this feature could be:  
  
```cpp  
#if define(__cpp_deduction_guides) && (__cpp_deduction_guides >= 201703L)  
namespace boost::safe_numerics {  
     template<class In> safe_base(In) -> safe_base<In>;  
}  
#endif  
```
