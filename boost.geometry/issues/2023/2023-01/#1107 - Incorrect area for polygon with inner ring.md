# #1107 - Incorrect area for polygon with inner ring [Closed]

> Username: cristy-rotaru  
> Created at: 2023-01-03 10:41:38 UTC  
> Updated at: 2023-01-03 11:35:07 UTC  
> Closed at: 2023-01-03 11:35:07 UTC  
> Url: https://github.com/boostorg/geometry/issues/1107  

I was using the following code to test some functionality:  
```cpp  
#include <iostream>  
  
#include <boost/geometry.hpp>  
  
namespace GEO = boost::geometry;  
  
using point2d_t   = GEO::model::d2::point_xy<double>;  
using polygon2d_t = GEO::model::polygon<point2d_t>;  
  
//-----------------------------------------------------------------------------------------------------  
int main() {  
//-----------------------------------------------------------------------------------------------------  
    polygon2d_t l_shape;  
    GEO::read_wkt("POLYGON((-4 0,0 4,4 0,0 -4,-4 0) (-1 1,1 1,1 -1,-1 -1,-1 1))", l_shape);  
    std::cout << GEO::area(l_shape) << std::endl;  
  
    return 0;  
}  
```  
Basically I am creating a polygon with the outer ring area of 32 and inner ring area of 4.  
According to the documentation I should get total area of 28. However I get 36.

---

## Comment 1

> Username: vissarion  
> Created at: 2023-01-03 11:35:07 UTC  
> Url: https://github.com/boostorg/geometry/issues/1107#issuecomment-1369666890  

The problem is that your polygon is invalid in [OGC standard](https://www.ogc.org/standards/sfa) terms. That is, the outer ring is clockwise (which is the default orientation in boost geometry) this is fine, but the inner ring is clockwise too but should the the reverse of the outer i.e. counterclockwise. The the result of the area is as expected. Note that boost geometry does not guarantee correct results for invalid input geometries. Also there is the `correct()` function that can be used to correct your input geometry.   
  
See https://www.boost.org/doc/libs/1_81_0/libs/geometry/doc/html/geometry/reference/concepts/concept_polygon.html for more
