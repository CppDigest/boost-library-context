# #255 - Error when emplacing in unordered_flat_map with std::atomic value [Closed]

> Username: OHNOalan  
> Created at: 2024-06-16 06:30:08 UTC  
> Updated at: 2024-06-16 17:33:08 UTC  
> Closed at: 2024-06-16 17:33:08 UTC  
> Url: https://github.com/boostorg/unordered/issues/255  

I encountered an error when trying to emplace an std::atomic value into a boost::unordered::unordered_flat_map.   
Here is a minimal reproducible example:  
```c++  
#include <boost/unordered/unordered_flat_map.hpp>  
#include <atomic>  
  
int main()  
{  
    boost::unordered::unordered_flat_map<int, std::atomic<int>> m;  
    m.try_emplace(1,0); // this is where complier complains  
  
    return 0;  
}  
```   
The above program throws an error when attempting to emplace an std::atomic value into the map. The compiler error message is as follows:  
```  
/libs/core/include/boost/core/allocator_access.hpp:529:5: error: no matching function for call to ‘std::pair<const int, std::atomic<int> >::pair(std::pair<int&&, std::atomic<int>&&>)’  
```

---

## Comment 1

> Username: joaquintides  
> Created at: 2024-06-16 14:28:33 UTC  
> Url: https://github.com/boostorg/unordered/issues/255#issuecomment-2171689354  

The problem you're experiencing is due to the fact that `boost::unordered_flat_map` requires that both [its key and mapped types be move constructible](https://www.boost.org/libs/unordered/doc/html/unordered.html#unordered_flat_map_description), and [`std::atomic` types are not move (or even copy) constructible](https://en.cppreference.com/w/cpp/atomic/atomic/atomic).  
  
There's a number of ways you can work around this:  
  
* You can use `boost::unordered_node_map`, which does not have the move constructibility requirement (but is slower than `boost::unordered_flat_map`).  
* You can replace `std::atomic` with a copyable substitute:  
  
```cpp  
#include <boost/unordered/unordered_flat_map.hpp>  
#include <atomic>  
  
struct copyable_atomic_int: std::atomic<int>  
{  
    using super = std::atomic<int>;  
    using super::super;  
    copyable_atomic_int(const copyable_atomic_int& x): super{x.load()} {}  
};  
  
int main()  
{  
    boost::unordered::unordered_flat_map<int, copyable_atomic_int> m;  
    m.try_emplace(1,0);  
  
    return 0;  
}  
```  
  
Good luck with your project,

---

## Comment 2

> Username: OHNOalan  
> Created at: 2024-06-16 17:30:10 UTC  
> Url: https://github.com/boostorg/unordered/issues/255#issuecomment-2171780127  

Thank you~
