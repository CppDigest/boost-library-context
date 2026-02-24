# #134 - flat_map over small_vector: MSVC compilation failure, but compiles fine in Clang and GCC [Closed]

> Username: vldt  
> Created at: 2019-12-04 23:52:48 UTC  
> Updated at: 2020-10-25 16:05:34 UTC  
> Closed at: 2020-10-25 16:05:34 UTC  
> Url: https://github.com/boostorg/container/issues/134  

The following code does not compile in MSVC 19.22 but compiles fine in Clang and/or GCC.  
  
Godbolt here: https://godbolt.org/z/QxQ6T5  
  
```  
#include <boost/container/small_vector.hpp>  
#include <boost/container/flat_map.hpp>  
  
struct point  
{  
    double x,y,z;  
};  
  
using pair_t = std::pair<int, point>;  
  
struct Data  
{  
    // Note: MSVC workaround commented out  
    using vector_t = boost::container::small_vector<std::pair<int, point>, 64 /*, boost::container::new_allocator<std::pair<int, point>>*/>;  
    using track_t = boost::container::flat_map<int, point, std::less<int>,vector_t>;   
  
    track_t track{};  
};  
  
int main()  
{  
   Data d;  
}  
  
```  
  
In MSVC, as a workaround, we have to explicitly specify the allocator for small_vector: https://godbolt.org/z/VvU4j2

---

## Comment 1

> Username: igaztanaga  
> Created at: 2020-10-25 16:05:34 UTC  
> Url: https://github.com/boostorg/container/issues/134#issuecomment-716171261  

I can't reproduce the bug with Boost 1.74. AFAIK it's still a compilation error in Goldbolt because it does not allow selecting the boost version for MSVC (but the VCPKG is version 202004 and it seems that boost 1.74 is still a work in progress in vcpkg  (https://github.com/microsoft/vcpkg/issues/13033).
