# #1004 - Compilation error [Closed]

> Username: fbriol  
> Created at: 2022-05-17 13:27:02 UTC  
> Updated at: 2022-05-19 09:32:34 UTC  
> Closed at: 2022-05-19 09:32:34 UTC  
> Url: https://github.com/boostorg/geometry/issues/1004  

The code below doesn't compile anymore with the 1.79 release. Tested with different versions of clang and g++. Theses compilers throw the same error:  
  
```  
/.../include/boost/geometry/strategies/geographic/distance_cross_track.hpp:212:13: error: 'auto' in return type deduced as 'float' here but deduced as 'double' in earlier return statement  
            return -g4 - pi/2;  
```  
  
```c++  
#include <boost/geometry.hpp>  
#include <map>  
  
namespace bg = boost::geometry;  
namespace bgi = boost::geometry::index;  
  
int main() {  
  using point_t = bg::model::point<float, 2, bg::cs::geographic<bg::degree> >;  
  using rtree_t = bgi::rtree<value_t, bgi::rstar<4> >;  
  
  auto rtree = rtree_t();  
  std::for_each(rtree.qbegin(bgi::nearest(point_t(-1.558444f, 52.38664f), 4)),  
                rtree.qend(), [](const auto &item) {});  
  return 0;  
}  
```

---

## Comment 1

> Username: awulkiew  
> Created at: 2022-05-17 14:15:27 UTC  
> Url: https://github.com/boostorg/geometry/issues/1004#issuecomment-1128926890  

Yes, it looks like either the `CT` return type should be explicitly specified or return values should be casted.  
https://github.com/boostorg/geometry/blob/develop/include/boost/geometry/strategies/geographic/distance_cross_track.hpp#L188  
  
@vissarion Is there a reason why `auto` is returned here? `CT` would be shorter.

---

## Comment 2

> Username: vissarion  
> Created at: 2022-05-18 09:44:17 UTC  
> Url: https://github.com/boostorg/geometry/issues/1004#issuecomment-1129799096  

Since the default type of floating point literals is `double` the return type deduced [here](https://github.com/boostorg/geometry/blob/develop/include/boost/geometry/strategies/geographic/distance_cross_track.hpp#L196) is `double` while [here](https://github.com/boostorg/geometry/blob/develop/include/boost/geometry/strategies/geographic/distance_cross_track.hpp#L212) is `float`.   
  
This is fixed in https://github.com/boostorg/geometry/pull/1005  
@awulkiew using `auto` as a return type is not a good choice here so I changed it to `CT`
