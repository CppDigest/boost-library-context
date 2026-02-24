# #1342 - intersection, wrong result [Closed]

> Username: barendgehrels  
> Created at: 2024-11-15 17:38:27 UTC  
> Updated at: 2024-11-18 17:10:30 UTC  
> Closed at: 2024-11-18 17:09:52 UTC  
> Url: https://github.com/boostorg/geometry/issues/1342  

> The original issue (#1295) is fixed but there's still a problem:  
> depending on the order of the input for boost::geometry::intersection the result might be wrong.  
> Could you please check the following example:  
  
  
  
```c++  
  
 #include "boost/geometry/algorithms/intersection.hpp"  
   
 #include "boost/geometry/algorithms/is_valid.hpp"  
   
 #include "boost/geometry/geometries/multi_polygon.hpp"  
   
 #include "boost/geometry/geometries/point_xy.hpp"  
   
 #include "boost/geometry/geometries/polygon.hpp"  
   
   
   
 #include <cassert>  
   
   
   
 int main(int, char *[])  
   
 {  
   
     using point_2d = boost::geometry::model::d2::point_xy<double>;  
   
     using BoostPolygon = boost::geometry::model::polygon<point_2d>;  
   
     using BoostMultiPolygon = boost::geometry::model::multi_polygon<BoostPolygon>;  
   
     using Ring = BoostPolygon::ring_type;  
   
   
   
     auto verifyIntersectionAreaIsLessOrEqualThanAreasOfInputPolygons = [](const auto& polygon1, const auto& polygon2)  
   
     {  
   
         assert(boost::geometry::is_valid(polygon1) && boost::geometry::is_valid(polygon2));  
   
         const auto areaOfPolygon1 = boost::geometry::area(polygon1);  
   
         const auto areaOfPolygon2 = boost::geometry::area(polygon2);  
   
         const double eps = 1e-7;  
   
         if (areaOfPolygon1 >= eps && areaOfPolygon2 >= eps)  
   
         {  
   
             BoostMultiPolygon intersection;  
   
             boost::geometry::intersection(polygon1, polygon2, intersection);  
   
             assert(boost::geometry::is_valid(intersection));  
   
             const auto areaOfIntersection = boost::geometry::area(intersection);  
   
             assert(areaOfIntersection < std::min(areaOfPolygon1, areaOfPolygon2) + eps);  
   
         }  
   
     };  
   
     {  
   
         // setting very small values to zero, begavior is expected  
   
         Ring polygon1{{0.0, 0.0},  
   
                       {2.000000861538488, 2.0000008},  
   
                       {23.527865484476326, 2.0000008},  
   
                       {25.349718032203803, 1.569919497715026},  
   
                       {26.0, 0.0},  
   
                       {0.0, 0.0}};  
   
         Ring polygon2{{2.000000861538488,  0.0},  
   
                       {2.000000861538488,  2.0000008},  
   
                       {23.527865484476326,  2.0000008},  
   
                       {23.527865484476326,  0.0},  
   
                       {2.000000861538488,  0.0}};  
   
         verifyIntersectionAreaIsLessOrEqualThanAreasOfInputPolygons(polygon2, polygon1);  
   
         verifyIntersectionAreaIsLessOrEqualThanAreasOfInputPolygons(polygon1, polygon2);  
   
     }  
   
     {  
   
         // with very small values the result might be wrong depending  
   
         // on the order of the input passed to boost::geometry::intersection  
   
         Ring polygon1{{0.0, 2.9447116290680494e-16},  
   
                       {2.000000861538488, 2.0000008},  
   
                       {23.527865484476326, 2.0000008},  
   
                       {25.349718032203803, 1.569919497715026},  
   
                       {26.0, 2.7755575615628914e-15},  
   
                       {0.0, 2.9447116290680494e-16}};  
   
         Ring polygon2{{2.000000861538488,  2.9447116290680494e-16},  
   
                       {2.000000861538488,  2.0000008},  
   
                       {23.527865484476326,  2.0000008},  
   
                       {23.527865484476326,  2.9447116290680494e-16},  
   
                       {2.000000861538488,  2.9447116290680494e-16}};  
   
         // works  
   
         verifyIntersectionAreaIsLessOrEqualThanAreasOfInputPolygons(polygon2, polygon1);  
   
         // switching the order the result is incorrect  
   
         verifyIntersectionAreaIsLessOrEqualThanAreasOfInputPolygons(polygon1, polygon2);  
   
     }  
   
     return 0;  
   
 }  
   
 ```   
  
 _Originally posted by @yaroslav-zlot in [#1295](https://github.com/boostorg/geometry/issues/1295#issuecomment-2301562064)_

---

## Comment 1

> Username: barendgehrels  
> Created at: 2024-11-15 20:42:48 UTC  
> Url: https://github.com/boostorg/geometry/issues/1342#issuecomment-2479891490  

According to my current code, all cases go wrong (also the one mentioned as `works`)

---

## Comment 2

> Username: barendgehrels  
> Created at: 2024-11-18 17:10:28 UTC  
> Url: https://github.com/boostorg/geometry/issues/1342#issuecomment-2483629996  

Fixed and merged. This issue was closed automatically.
