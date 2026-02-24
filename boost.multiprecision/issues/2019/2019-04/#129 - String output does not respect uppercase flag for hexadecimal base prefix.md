# #129 - String output does not respect uppercase flag for hexadecimal base prefix [Closed]

> Username: pavelkryukov  
> Created at: 2019-04-27 21:57:40 UTC  
> Updated at: 2024-06-18 10:36:19 UTC  
> Closed at: 2019-05-01 13:16:09 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/129  

Consider following program:  
  
```c++  
#include <cstdint>  
#include <boost/multiprecision/cpp_int.hpp>  
  
int main()  
{  
    uint64_t val64 = 0xf;  
    boost::multiprecision::uint128_t val128 = 0xf;  
    std::cout << std::hex << std::uppercase << std::showbase  
              << "uint64: "  << val64 << std::endl  
              << "uint128: " << val128 << std::endl;  
    return 0;  
}  
```  
  
The output is:  
```  
uint64: 0XF  
uint128: 0xF  
```  
  
The expected output is (note the uppercase for `0x`):  
```  
uint64: 0XF  
uint128: 0XF  
```

---

## Comment 1

> Username: KingBodo  
> Created at: 2024-06-18 10:36:17 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/129#issuecomment-2175777616  

> Consider following program:  
>   
> ```c++  
> #include <cstdint>  
> #include <boost/multiprecision/cpp_int.hpp>  
>   
> int main()  
> {  
>     uint64_t val64 = 0xf;  
>     boost::multiprecision::uint128_t val128 = 0xf;  
>     std::cout << std::hex << std::uppercase << std::showbase  
>               << "uint64: "  << val64 << std::endl  
>               << "uint128: " << val128 << std::endl;  
>     return 0;  
> }  
> ```  
>   
> The output is:  
>   
> ```  
> uint64: 0XF  
> uint128: 0xF  
> ```  
>   
> The expected output is (note the uppercase for `0x`):  
>   
> ```  
> uint64: 0XF  
> uint128: 0XF  
> ```
