# #1201 - bg::intersection result is the second input polygon instead of empty polygon [Open]

> Username: snowman791  
> Created at: 2023-09-14 10:38:25 UTC  
> Updated at: 2024-11-24 10:04:21 UTC  
> Url: https://github.com/boostorg/geometry/issues/1201  

on version 1.83 the bg::Intersection generates wrong result for this specific case.  The result polygon is same as the second input polygon Q.  
When the first polygon P has instead of the value 1e-14 eg. 1e-13 or 1e-15, the result is empty, which is correct.  
  
using boost_point_2d = boost::geometry::model::d2::point_xy;  
using boost_polygon_2d = boost::geometry::model::polygon<boost_point_2d, false>;  
using boost_multipolygon_2d = boost::geometry::model::multi_polygon<boost_polygon_2d>;  
  
boost_polygon_2d P = {{{1e-14, 0}, {-10, 50}, {-20, 50},   {1e-14, 0}}};  
boost_polygon_2d Q = {{{0, 1000},  {0, 0},   {1000, 0},  {1000, 1000},  {0, 1000}}};  
boost_multipolygon_2d R;  
  
bg::intersection(P, Q, R);  
![image](https://github.com/boostorg/geometry/assets/73174595/3b7814b7-ede1-4ec5-a457-cf10e4e65821)

---

## Comment 1

> Username: vissarion  
> Created at: 2023-12-01 14:48:04 UTC  
> Url: https://github.com/boostorg/geometry/issues/1201#issuecomment-1836239012  

A side note: the correct result is not empty, it is a triangle   
  
 MULTIPOLYGON(((0 p1,1e-14 0,0 p2,0 p1)))  
  
where p1, p2 are intersection points of the segments (-10 50, 1e-14 0) and (-20 50, 1e-14 0) with segment (0 0, 0 1000).  
  
So, I think both results you get are wrong.
