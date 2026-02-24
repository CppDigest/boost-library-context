# #314 - Pretty printer error printing flat map [Closed]

> Username: jarro2783  
> Created at: 2025-08-06 21:48:06 UTC  
> Updated at: 2025-08-22 08:15:50 UTC  
> Closed at: 2025-08-22 08:15:50 UTC  
> Url: https://github.com/boostorg/unordered/issues/314  

Boost: 1.88.0  
GCC: 15.1  
  
In the basic example below that uses `unordered_flat_map`, and breaking in gdb at the `cout`, gdb fails to print the contents of the map.  
  
```  
#include <boost/unordered/unordered_flat_map.hpp>  
  
#include <iostream>  
  
int main()  
{  
  boost::unordered_flat_map<int, int> map;  
  
  map.emplace(1, 2);  
  map.emplace(3, 5);  
  map.emplace(5, 6);  
  
  std::cout << map.size() << std::endl;  
}  
```  
  
```  
(gdb) print map  
$1 = boost::unordered_flat_map with 3 elements = {[5] = 6Python Exception <class 'gdb.error'>: There is no member or method named N.  
```  
  
It seems like gcc might be compiling out the `static constexpr` member `N` in the `group15` class.

---

## Comment 1

> Username: k3DW  
> Created at: 2025-08-06 21:52:56 UTC  
> Url: https://github.com/boostorg/unordered/issues/314#issuecomment-3161750785  

Thanks for the bug report. I'll take a look.

---

## Comment 2

> Username: jarro2783  
> Created at: 2025-08-06 21:54:56 UTC  
> Url: https://github.com/boostorg/unordered/issues/314#issuecomment-3161754242  

Also I just checked clang and it works fine.
