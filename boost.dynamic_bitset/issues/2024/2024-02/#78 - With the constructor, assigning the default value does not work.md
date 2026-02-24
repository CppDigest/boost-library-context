# #78 - With the constructor, assigning the default value does not work ? [Closed]

> Username: Jxtopher  
> Created at: 2024-02-14 20:20:33 UTC  
> Updated at: 2024-11-26 21:07:47 UTC  
> Closed at: 2024-11-26 21:07:45 UTC  
> Url: https://github.com/boostorg/dynamic_bitset/issues/78  

Hello,  
  
Consider the following code:   
```cpp  
#include <boost/dynamic_bitset.hpp>  
#include <iostream>  
  
int main() {  
    auto bitset = boost::dynamic_bitset<>(10, true);  
    std::cout << bitset << std::endl;  
    return 0;  
}  
```  
  
The result I expected:  
```  
1111111111  
```  
  
But the result is  
```  
0000000001  
```  
This behavior is it normal ?  
  
On Debian with libboost1.74-dev:amd64.   
  
Thanks in advance

---

## Comment 1

> Username: jeking3  
> Created at: 2024-11-26 21:07:45 UTC  
> Url: https://github.com/boostorg/dynamic_bitset/issues/78#issuecomment-2501937294  

This is functioning as designed.  The documentation states that using a value in the constructor with this signature takes an unsigned long, and a conversion from bool is allowed.  If you compile with a newer compiler or pedantic settings, it may warn you about it.
