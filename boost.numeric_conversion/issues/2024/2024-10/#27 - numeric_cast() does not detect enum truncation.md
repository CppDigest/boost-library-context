# #27 - numeric_cast() does not detect enum truncation [Open]

> Username: pkl97  
> Created at: 2024-10-22 20:25:59 UTC  
> Updated at: 2024-10-22 20:25:59 UTC  
> Url: https://github.com/boostorg/numeric_conversion/issues/27  

This simple program demonstrates the problem. The conversion below succeeds but the converted value is truncated. I would expect an exception in this case.  
  
```c++  
#include <iostream>  
#include <boost/numeric/conversion/cast.hpp>  
  
int main()  
{  
    enum E : unsigned int { e=1 };  
    const std::uint64_t value=0x1'8000'0000;  
    std::cout << std::hex << boost::numeric_cast<E>(value) << std::endl; // prints "80000000"  
    return 0;  
}  
```  
  
The program writes 80000000 instead of 180000000 to the console, so bit 32 (when numbering them from 0) is lost.  
  
The problem does not occur (i.e. an exception is thrown) if the value is directly cast to the underlying type "unsigned int" of the enum.
