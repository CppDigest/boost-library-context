# #318 - [BUG ???] boost::unordered_node_map --> does not seem to have iterator stability... [Closed]

> Username: nbooster  
> Created at: 2025-10-08 04:13:27 UTC  
> Updated at: 2025-10-08 04:43:40 UTC  
> Closed at: 2025-10-08 04:20:04 UTC  
> Url: https://github.com/boostorg/unordered/issues/318  

Hello,  
  
I run this program and I get seg fault (g++ 15.2 | Boost 1.87):  
  
```cpp  
#include <iostream>  
#include <boost/unordered/unordered_node_map.hpp>  
  
// This program causes SIGSEGV, even it should not,  
// because boost::unordered_node_map is supposed to have  
// iterator stability... (unlike boost::unordered_flat_map).  
  
int main()  
{  
    using UMapType = boost::unordered_node_map<int, int>;  
  
    UMapType UM;  
  
    std::vector<UMapType::iterator> Hits;  
  
    int N = 100;  
  
    for ( int i{0}; i < N; ++i )  
    {  
        const auto& [hit, inserted] = UM.try_emplace(i, i);  
          
        Hits.push_back(hit);  
    }  
  
    for ( const auto& hit : Hits )  
        UM.erase(hit);  
  
    // UM.erase(Hits.front(), Hits.back()); // <-- this causes segfault too...  
  
    std::cout << "SUCCESS !!!" << std::endl;  
  
    return 0;  
}  
```  
  
Is this normal for this container ?  
  
https://godbolt.org/z/h6oTWoK4q

---

## Comment 1

> Username: k3DW  
> Created at: 2025-10-08 04:29:40 UTC  
> Url: https://github.com/boostorg/unordered/issues/318#issuecomment-3379527226  

Note that this container has pointer stability, not iterator stability. You can store the address of your elements safely, but not the iterators.  
  
From [the docs for this function](https://www.boost.org/doc/libs/latest/libs/unordered/doc/html/unordered/reference/unordered_node_map.html#unordered_node_map_try_emplace):  
  
> Can invalidate iterators, but only if the insert causes the load to be greater than the maximum load.

---

## Comment 2

> Username: nbooster  
> Created at: 2025-10-08 04:32:02 UTC  
> Updated at: 2025-10-08 04:43:40 UTC  
> Url: https://github.com/boostorg/unordered/issues/318#issuecomment-3379530805  

Yes, thanks I just saw it... that's why I closed it immediately...  
  
Would be great if we could get an iterator from the element pointer though (like in intrusive containers)...  
or even better to get an intrusive unordered_map...  
  
*Iterators of these containers are 16 bytes in size (instead of 8 like all the other containers that I have seen...).
