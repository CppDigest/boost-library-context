# #108 - fatal error for 1_82 [Closed]

> Username: afabri  
> Created at: 2023-06-29 15:59:51 UTC  
> Updated at: 2023-06-29 16:09:18 UTC  
> Closed at: 2023-06-29 16:09:18 UTC  
> Url: https://github.com/boostorg/property_tree/issues/108  

With this code  
  
```cpp  
#include <boost/property_tree/ptree.hpp>  
  
int main()  
{  
    boost::property_tree::ptree ptr;  
    ptr.put("name", "toto");  
    return 0;  
}  
```  
  
I get the error `boost-1_82\boost/core/swap.hpp(81,5): fatal  error C1202: recursive type or function dependency context too complex ` for Visual 2022.

---

## Comment 1

> Username: afabri  
> Created at: 2023-06-29 16:09:17 UTC  
> Url: https://github.com/boostorg/property_tree/issues/108#issuecomment-1613472094  

I was a little bit too quick.
