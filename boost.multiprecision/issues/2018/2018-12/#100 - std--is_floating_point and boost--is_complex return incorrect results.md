# #100 - std::is_floating_point and boost::is_complex return incorrect results [Closed]

> Username: NAThompson  
> Created at: 2018-12-06 05:31:41 UTC  
> Updated at: 2018-12-06 16:54:36 UTC  
> Closed at: 2018-12-06 09:03:19 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/100  

Code to reproduce:  
  
```cpp  
#include <iostream>  
#include <boost/type_traits.hpp>  
#include <boost/multiprecision/cpp_bin_float.hpp>  
#include <boost/multiprecision/cpp_complex.hpp>  
  
using boost::multiprecision::cpp_bin_float_50;  
using boost::multiprecision::cpp_complex_50;  
  
  
int main()  
{  
    if(boost::is_complex<cpp_complex_50>::value)  
    {  
        std::cout << "cpp_complex_50 is complex\n";  
    }  
    else  
    {  
        std::cout << "cpp_complex_50 is NOT complex\n";  
    }  
  
    using std::is_floating_point;  
    if (is_floating_point<cpp_bin_float_50>::value)  
    {  
        std::cout << "cpp_bin_float_50 is a floating point\n";  
    }  
    else  
    {  
        std::cout << "cpp_bin_float_50 is NOT floating point\n";  
    }  
}  
```  
  
Output:  
  
```bash  
cpp_complex_50 is NOT complex  
cpp_bin_float_50 is NOT floating point  
```

---

## Comment 1

> Username: jzmaddock  
> Created at: 2018-12-06 09:03:19 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/100#issuecomment-444798720  

That's the correct behaviour, if not helpful in this instance.  
  
To clarify: if we take is_floating_point - it is true only when the type is a floating point type _as specified by the C++ type system_ (ie float double or long double), it is never true for class types.  Likewise is_complex is true only for instances of std::complex<T> which is not the case here.  Basically type_traits tells you about the type system, not the _behaviour_ of the type, and multiprecision types are _class_ types (as far as the type system is concerned), not integer, float, void, pointer, function or reference types.  
  
Multiprecision has some traits classes of it's own to categorise it's types (number_category for example), or you could numeric_limits to "spot" class types that are "number-like".  
  
Ideally we would have concepts to deal with this, but thinking about it, now that we have is_detected, we could compose some additional type_traits to correctly (and auto-magically) handle classes that are numbers.

---

## Comment 2

> Username: NAThompson  
> Created at: 2018-12-06 16:54:36 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/100#issuecomment-444945402  

Cool, that works. For my own memory and whoever else stumbles on this, what John is talking about with `number_category`:  
  
```cpp  
#include <boost/multiprecision/detail/number_base.hpp>  
boost::multiprecision::number_category<cpp_bin_float_50>::value == boost::multiprecision::number_kind_floating_point  
```  
  
tests for floating point, and  
  
```cpp  
boost::multiprecision::number_category<cpp_complex_50>::value == boost::multiprecision::number_kind_complex  
```  
  
tests for complex.
