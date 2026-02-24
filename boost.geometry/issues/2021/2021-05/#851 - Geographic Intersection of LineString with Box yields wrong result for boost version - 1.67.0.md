# #851 - Geographic Intersection of LineString with Box yields wrong result for boost version > 1.67.0 [Closed]

> Username: ghost  
> Created at: 2021-05-21 14:31:53 UTC  
> Updated at: 2021-05-26 07:49:06 UTC  
> Closed at: 2021-05-26 07:49:06 UTC  
> Url: https://github.com/boostorg/geometry/issues/851  

The following code  
  
``` c++  
#include <iostream>  
#include <boost/geometry.hpp>  
#include <boost/geometry/core/coordinate_system.hpp>  
#include <boost/geometry/geometries/point_xy.hpp>  
  
namespace bg = boost::geometry;  
namespace bm = bg::model::d2;  
  
using Pt = bm::point_xy<double, bg::cs::geographic<bg::degree> >;  
using Box = bg::model::box<Pt>;  
using LineString = bg::model::linestring<Pt>;  
  
 Pt Stuttgart() { return { 9.18, 48.78}; }  
 Pt Berlin() { return { 13.4, 52.52 }; }  
 Box boxInCzechRepublic() {  
 return { {13.381347, 50.625}  
        , {13.40332, 50.646972}};  
}  
  
LineString StuttgartBerlin() {  
    return {Stuttgart(), Berlin()};  
}  
  
  
int main() {  
  bool const result = bg::intersects (StuttgartBerlin(), boxInCzechRepublic());  
  if (result) {  
    std::cout << "The line from Stuttgart to Berlin intersects the Czech Republic" << std::endl;  
  }  
  else {  
    std::cout << "The line from Stuttgart to Berlin does not intersect the Czech Republic" << std::endl;  
  }  
  return 0;  
}  
```  
  
yields the correct result for boost version <= 1.67 and the wrong result for boost version >= 1.68:  
  
- version 1.67.0: https://godbolt.org/z/fMxEjo86P  
- version 1.68.0: https://godbolt.org/z/nhbqE9sz8  
- (...)   
- version 1.76.0: https://godbolt.org/z/r1r8r6YhY  
  
I also observed this behavior for other compilers.

---

## Comment 1

> Username: awulkiew  
> Created at: 2021-05-23 13:43:43 UTC  
> Updated at: 2021-05-23 13:47:36 UTC  
> Url: https://github.com/boostorg/geometry/issues/851#issuecomment-846565494  

Thanks for the report!  
  
I can reproduce it. The algorithm returns `true` for the case:  
  
![obraz](https://user-images.githubusercontent.com/1226951/119262664-4a60f700-bbdc-11eb-8034-a342cbe36512.png)  
  
The issue seems to be located in `disjoint_segment_box_sphere_or_spheroid`, somewhere around these lines:  
https://github.com/boostorg/geometry/blob/develop/include/boost/geometry/algorithms/detail/disjoint/segment_box.hpp#L206-L213  
`vertex_lat` is the same as the latitude of `box_seg`'s min/max only if min/max is lower/higher than both of the endpoints.  
  
I also don't know why doesn't the function return when all of the azimuth sides are the same here:  
https://github.com/boostorg/geometry/blob/develop/include/boost/geometry/algorithms/detail/disjoint/segment_box.hpp#L184-L192  
  
@vissarion What do you think?
