# #286 - devector assertion failure [Closed]

> Username: jrade  
> Created at: 2024-07-20 13:59:12 UTC  
> Updated at: 2024-07-20 15:40:00 UTC  
> Closed at: 2024-07-20 15:40:00 UTC  
> Url: https://github.com/boostorg/container/issues/286  

When I run the following program in debug mode  
```  
#include <boost/container/devector.hpp>  
  
int main()  
{  
    boost::container::devector<int> dev;  
    dev.push_front(0);  
    dev.pop_back();  
    dev.push_front(0);  
}  
```  
the following assertion is triggered  
```  
Assertion failed: should_move_back(p) ? (back_free_cap < n) : (front_free_cap < n),  
file C:\Libraries\boost_1_85_0\boost\container\devector.hpp, line 2658  
```  
I am using Boost 1.85.0 and Visual Studio 2022 (ver. 17.10.4) on Windows 11.

---

## Comment 1

> Username: jrade  
> Created at: 2024-07-20 15:40:00 UTC  
> Url: https://github.com/boostorg/container/issues/286#issuecomment-2241187347  

Seems to be the same as https://github.com/boostorg/container/issues/285
