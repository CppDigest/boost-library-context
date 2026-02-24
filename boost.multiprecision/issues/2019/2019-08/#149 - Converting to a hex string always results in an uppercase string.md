# #149 - Converting to a hex string always results in an uppercase string [Closed]

> Username: antouhou  
> Created at: 2019-08-04 19:15:38 UTC  
> Updated at: 2020-01-29 19:43:27 UTC  
> Closed at: 2020-01-29 19:43:27 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/149  

Consider the following program:  
```c++  
#include <cstdint>  
#include <boost/multiprecision/cpp_int.hpp>  
  
int main()  
{  
    uint64_t val64 = 0xffff;  
    boost::multiprecision::uint128_t val128 = 0xffff;  
    std::cout << std::hex << std::showbase  
              << "uint64: "  << val64 << std::endl  
              << "uint128: " << val128 << std::endl;  
  
    return 0;  
}  
```  
Expected result:  
```  
uint64: 0xffff  
uint128: 0xffff  
```  
Actual result:  
```  
uint64: 0xffff  
uint128: 0xFFFF  
```  
I'm using boost 1.70.0 and GCC 8.3.0 on Ubuntu 18.04

---

## Comment 1

> Username: ram-nad  
> Created at: 2020-01-28 21:40:06 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/149#issuecomment-579469258  

Sir, i think this issue is fixed. I tested it on my computer using version Boost 1.72.0, G++ 7.4.0 on Ubuntu 18.04 and it gives correct results.  
  
```cpp  
#include <cstdint>  
#include <boost/multiprecision/cpp_int.hpp>  
  
int main()  
{  
    uint64_t val64 = 0xffff;  
    boost::multiprecision::uint128_t val128 = 0xffff;  
    std::cout << std::hex << std::showbase  
              << "uint64: "  << val64 << std::endl  
              << "uint128: " << val128 << std::endl  
              << "uint128: " << std::uppercase << val128 << std::endl;  
  
    return 0;  
}  
```  
  
Output:  
```shell  
uint64: 0xffff  
uint128: 0xffff  
uint128: 0XFFFF  
```

---

## Comment 2

> Username: jzmaddock  
> Created at: 2020-01-29 19:43:27 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/149#issuecomment-579926860  

Yup, fixed a while back in https://github.com/boostorg/multiprecision/commit/afc48ebc53640c5753be61a5dd93828c267a60fc.
