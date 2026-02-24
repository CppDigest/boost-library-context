# #1037 - Bit_ceil [Open]

> Username: baldapps  
> Created at: 2025-05-09 04:30:42 UTC  
> Updated at: 2025-09-01 20:31:29 UTC  
> Url: https://github.com/boostorg/boost/issues/1037  

According to the specifications, bit_ceil should return 1 with 0 input, but it returns 0.

---

## Comment 1

> Username: Pega5us  
> Created at: 2025-06-06 07:21:44 UTC  
> Url: https://github.com/boostorg/boost/issues/1037#issuecomment-2948339624  

yeah. It should match the boost doc (and std::bit_ceil) behaviour.  
Boost doc: [here](https://live.boost.org/doc/libs/1_85_0/libs/core/doc/html/core/bit.html#core.bit.header_boost_core_bit_hpp.integral_powers_of_2)  
  
```cpp  
#include <iostream>  
#include <boost/core/bit.hpp>  
#include <bit>  
  
int main() {  
    std::cout << "boost::core::bit_ceil(0u) = " << boost::core::bit_ceil(0u) << "\n";  
    std::cout << "boost::core::bit_ceil(5u) = " << boost::core::bit_ceil(5u) << "\n";  
    std::cout << "std::bit_ceil(0u) = " << std::bit_ceil(0u) << "\n";    
    std::cout << "std::bit_ceil(5u) = " << std::bit_ceil(5u) << "\n";    
}  
```   
```text  
Output:  
boost::core::bit_ceil(0u) = 0  
boost::core::bit_ceil(5u) = 8  
std::bit_ceil(0u) = 1  
std::bit_ceil(5u) = 8  
```  
Godbolt: https://godbolt.org/z/M341McEcb

---

## Comment 2

> Username: Pega5us  
> Created at: 2025-06-06 16:13:22 UTC  
> Url: https://github.com/boostorg/boost/issues/1037#issuecomment-2949768530  

This is fixed.

---

## Comment 3

> Username: nigels-com  
> Created at: 2025-09-01 20:31:29 UTC  
> Url: https://github.com/boostorg/boost/issues/1037#issuecomment-3243216850  

Likely fixed in boost 1.89.0.  
Can anyone confirm?
