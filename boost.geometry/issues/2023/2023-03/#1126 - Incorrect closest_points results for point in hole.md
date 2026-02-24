# #1126 - Incorrect closest_points results for point in hole [Closed]

> Username: rconde01  
> Created at: 2023-03-29 14:27:07 UTC  
> Updated at: 2023-03-29 19:42:56 UTC  
> Closed at: 2023-03-29 19:40:28 UTC  
> Url: https://github.com/boostorg/geometry/issues/1126  

I found this while playing around with adding support for large spherical polygons.  
  
https://godbolt.org/z/9xex4a4E5  
  
```  
#include <iostream>  
  
#include <boost/geometry.hpp>  
#include <boost/geometry/geometries/point_xy.hpp>  
#include <boost/geometry/geometries/polygon.hpp>  
  
namespace bg = boost::geometry;  
  
using SphericalPoint =  
bg::model::point<double, 2, bg::cs::spherical_equatorial<bg::degree>>;  
  
using SphericalPolygon =  
bg::model::polygon<SphericalPoint,  
   false,  // clockwise if true  
   true,   // closed  
   std::vector, std::vector, std::allocator,std::allocator>;  
  
using SphericalSegment = bg::model::segment<SphericalPoint>;  
  
  
auto main() -> int {  
    SphericalPoint point {0.4,0.4};  
  
    {  
        SphericalPolygon polygon{{{0,0},{1,0},{0,1},{0,0}}};  
  
        bg::correct(polygon);  
  
        SphericalSegment segment;  
  
        bg::closest_points(point,polygon,segment,boost::geometry::strategies::closest_points::spherical<double,void>());  
  
        std::cout << bg::wkt(segment) << std::endl;  
    }  
  
    {  
        SphericalPolygon polygon;  
        polygon.outer() = {{-0.5,-0.5},{1.5,0},{0,1.5},{-0.5,-0.5}};  
        polygon.inners().push_back({{0,0},{1,0},{0,1},{0,0}});  
  
        bg::correct(polygon);  
  
        SphericalSegment segment;  
  
        bg::closest_points(point,polygon,segment,boost::geometry::strategies::closest_points::spherical<double,void>());  
  
        std::cout << bg::wkt(segment) << std::endl;  
    }  
  
    return 0;  
}  
```  
  
I believe the answers should be the same, but you get:  
  
```  
LINESTRING(0.4 0.4,0.4 0.4)  
LINESTRING(0.4 0.4,0.500033 0.500024)  
```

---

## Comment 1

> Username: awulkiew  
> Created at: 2023-03-29 19:32:40 UTC  
> Updated at: 2023-03-29 19:35:59 UTC  
> Url: https://github.com/boostorg/geometry/issues/1126#issuecomment-1489187019  

In the first case point is inside polygon so both endpoints of the segment are equal.  
  
![image](https://user-images.githubusercontent.com/1226951/228647195-8a9b367c-ab29-4ee2-b9cd-f0ea4fbaca4f.png)  
  
In the second case point is inside a hole, and the result is the shortest segment between the point and the hole.  
  
![image](https://user-images.githubusercontent.com/1226951/228647639-fc634a21-21cb-4279-ac6f-e4a31a57b2e2.png)  
  
Btw, initially the order of points in the hole is incorrect. Is is then reversed in `bg::correct()`.

---

## Comment 2

> Username: rconde01  
> Created at: 2023-03-29 19:40:28 UTC  
> Updated at: 2023-03-29 19:42:56 UTC  
> Url: https://github.com/boostorg/geometry/issues/1126#issuecomment-1489196274  

Ah...I see...it's not the distance to the edge.  
  
Yes - correct fixes the hole.
