# #106 - gcc 14+ gives wrong answer for mp_map_find [Closed]

> Username: brevzin  
> Created at: 2025-05-07 17:38:38 UTC  
> Updated at: 2025-05-08 13:51:49 UTC  
> Closed at: 2025-05-08 13:51:49 UTC  
> Url: https://github.com/boostorg/mp11/issues/106  

This example compiles on gcc 12, gcc 13, and clang. But not on gcc 14 or gcc 15:  
  
```cpp  
#include <boost/mp11.hpp>  
using namespace boost::mp11;  
  
template<typename T>  
struct Wrap {  
    struct type{};  
};  
  
using Int = typename Wrap<int>::type;  
using Long = typename Wrap<long>::type;  
  
using Map = mp_list<  
    mp_list<Int, void>,  
    mp_list<Long, void>  
>;  
  
using Lookup = mp_map_find<Map, Int>;  
  
static_assert(!std::is_same<Lookup, void>::value);  
```  
  
I opened this as a [gcc bug](https://gcc.gnu.org/bugzilla/show_bug.cgi?id=120161), but in the meantime it would be nice to get a workaround here.
