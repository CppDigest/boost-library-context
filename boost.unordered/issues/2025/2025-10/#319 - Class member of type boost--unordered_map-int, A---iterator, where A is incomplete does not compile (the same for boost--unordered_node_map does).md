# #319 - Class member of type boost::unordered_map<int, A>::iterator, where A is incomplete does not compile (the same for boost::unordered_node_map does) [Closed]

> Username: nbooster  
> Created at: 2025-10-08 18:47:53 UTC  
> Updated at: 2025-10-12 22:47:24 UTC  
> Closed at: 2025-10-12 11:15:33 UTC  
> Url: https://github.com/boostorg/unordered/issues/319  

Hello,  
  
is it possible to have an iterator of boost::unordered_map<int, A>  
where A is an incomplete type, as a class member ?  
  
*It is for boost::unordered_node_map and boost::unordered_flat_map...  
  
The following code works, except if we uncomment the commented line...  
  
```cpp  
#include <iostream>  
#include <boost/unordered/unordered_map.hpp>  
#include <boost/unordered/unordered_node_map.hpp>  
  
// Class member of boost::unordered_node_map iterator, with incomplete type, works but  
// the same for a boost::unordered_map iterator does not.  
  
class A;  
  
using iterator =   
//boost::unordered_map<int, A>::iterator;   // <-- uncomment this, and comment the line beyond, to observe the behavour...  
boost::unordered_node_map<int, A>::iterator;  
  
class B  
{  
    iterator it;  
};  
  
class A  
{  
    int i;  
};  
  
int main()  
{  
    // I am not even instantiating a var of B type here...  
  
    std::cout << "SUCCESS !!!" << std::endl;  
  
    return 0;  
}  
```  
  
https://godbolt.org/z/YssYnvjca

---

## Comment 1

> Username: joaquintides  
> Created at: 2025-10-12 11:15:33 UTC  
> Url: https://github.com/boostorg/unordered/issues/319#issuecomment-3394182999  

Functionality implemented at #321.

---

## Comment 2

> Username: nbooster  
> Created at: 2025-10-12 22:47:24 UTC  
> Url: https://github.com/boostorg/unordered/issues/319#issuecomment-3395437677  

Thanks for the fix.
