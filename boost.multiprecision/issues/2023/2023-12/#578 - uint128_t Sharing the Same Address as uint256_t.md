# #578 - uint128_t Sharing the Same Address as uint256_t [Closed]

> Username: stngores  
> Created at: 2023-12-15 17:58:19 UTC  
> Updated at: 2023-12-15 18:27:25 UTC  
> Closed at: 2023-12-15 18:27:25 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/578  

I had hoped I could map some variables to the same address but apply different precisions. This seems achievable with uint64_t, uint32_t, uint16_t, and uint8_t.  In the example below, the variables "a", "b", "c", "d" have the same address but different precision.  Initializing "a" also initializes "b", "c", and "d".  The common address and their individual values are shown in the results below.  
  
I'm having problems doing the same thing with uint256_t and uint128_t. In the example below, the variables "x", "y", "z" are mapped to the same address and only "x" is assigned a new value.  Unfortunately "y" does not get initialized properly, and will fail differently depending on the number of digits defined in "x".  The results printed for "y" show it with no digits.  I commented out the "std::cout" code for "y" that generated an access violation, and the one that threw "std::bad_alloc".  The 16-bit variable "z" mapped to the 256-bit variable "x" seems to be OK.  
  
Is there a way to make this work for uint256_t and uint128_t as easily as for uint64_t, uint32_t, uint16_t and uint8_t?  
  
///////////////////////////////////////////////////////////////////////////  
#include <iostream>  
#include <boost/multiprecision/cpp_int.hpp>  
  
uint64_t  a = 0;  
uint32_t& b = * (uint32_t*)&a;  
uint16_t& c = * (uint16_t*)&a;  
uint8_t&  d = * (uint8_t*)&a;  
  
boost::multiprecision::uint256_t x = 0;  
boost::multiprecision::uint128_t& y = * (boost::multiprecision::uint128_t*)&x;  
uint16_t& z = * (uint16_t*)&x;  
  
int main()  
{  
    a = 0x0123456789ABCDEF;  
    //printf("&a=%llx, &b=%llx, &c=%llx, &d=%llx, a=%llx, b=%llx, c=%llx, d=%llx\n", &a, &b, &c, &d, a, b, c, d);  
    std::cout << "&a=0x" << std::hex << &a << ", a=0x" << std::hex << a << std::endl;  
    std::cout << "&b=0x" << std::hex << &b << ", b=0x" << std::hex << b << std::endl;  
    std::cout << "&c=0x" << std::hex << &c << ", c=0x" << std::hex << c << std::endl;  
    //std::cout << "&d=0x" << std::hex << &d << ", d=0x" << std::hex << d << std::endl;  
    printf("&d=0x%llx, d=0x%llx\n", &d, d);         // chose printf here instead of "std::cout"  
  
    // constructor can convert a string.   
    x = boost::multiprecision::uint256_t("0x0123456789ABCDEF");  
    std::cout << "&x=0x" << std::hex << &x << ", x=0x" << std::hex << x << std::endl;  
    std::cout << "&y=0x" << std::hex << &y << ", y=0x" << std::hex << y << std::endl;   // empty results  
    std::cout << "&z=0x" << std::hex << &z << ", z=0x" << std::hex << z << std::endl;  
  
    x = boost::multiprecision::uint256_t("0x1111111111111111222222220123456789ABCDEF");  
    std::cout << "&x=0x" << std::hex << &x << ", x=0x" << std::hex << x << std::endl;  
    // COMMENTED the following line to generate results below  
    std::cout << "&y=0x" << std::hex << &y << ", y=0x" << std::hex << y << std::endl;   // generates access violation  
    std::cout << "&z=0x" << std::hex << &z << ", z=0x" << std::hex << z << std::endl;  
  
    x = boost::multiprecision::uint256_t("0x1111111111111111222222222222222233333333333333330123456789ABCDEF");  
    std::cout << "&x=0x" << std::hex << &x << ", x=0x" << std::hex << x << std::endl;  
    // COMMENTED the following line to generate results below  
    std::cout << "&y=0x" << std::hex << &y << ", y=0x" << std::hex << y << std::endl;   // throws std::bad_alloc  
    std::cout << "&z=0x" << std::hex << &z << ", z=0x" << std::hex << z << std::endl;  
}  
///////////////////////////////////////////////////////////////////////////  
Output from above program:  
&a=0x00007FF67042E680, a=0x123456789abcdef  
&b=0x00007FF67042E680, b=0x89abcdef  
&c=0x00007FF67042E680, c=0xcdef  
&d=0x7ff67042e680, d=0xef  
&x=0x00007FF67042E018, x=0x123456789abcdef  
&y=0x00007FF67042E018, y=0x  
&z=0x00007FF67042E018, z=0xcdef  
&x=0x00007FF67042E018, x=0x1111111111111111222222220123456789abcdef  
&z=0x00007FF67042E018, z=0xcdef  
&x=0x00007FF67042E018, x=0x1111111111111111222222222222222233333333333333330123456789abcdef  
&z=0x00007FF67042E018, z=0xcdef

---

## Comment 1

> Username: jzmaddock  
> Created at: 2023-12-15 18:27:25 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/578#issuecomment-1858316194  

Not only can you not do that with multiprecision types, but what you're doing with built in types results in undefined behaviour, and may or may not work depending on the processor architecture, the compiler, and what build options are used.  See https://en.wikipedia.org/wiki/Type_punning.
