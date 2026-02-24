# #1233 - buffer returns an invalid result [Open]

> Username: vissarion  
> Created at: 2024-01-26 13:09:55 UTC  
> Updated at: 2024-01-27 10:47:38 UTC  
> Url: https://github.com/boostorg/geometry/issues/1233  

The following returns an invalid result in 1.84 (while returning `MULTIPOLYGON()` in `1.78`).  
  
```cpp  
#include <boost/geometry.hpp>  
  
int main() {  
  
  
    using Point = boost::geometry::model::d2::point_xy<double>;  
    using Polygon = boost::geometry::model::polygon<Point>;  
    using Linestring = boost::geometry::model::linestring<Point>;  
    using MultiPolygon = boost::geometry::model::multi_polygon<Polygon>;  
    using MultiLinestring = boost::geometry::model::multi_linestring<Linestring>;  
  
    boost::geometry::strategy::buffer::join_round join_strategy(36);  
    boost::geometry::strategy::buffer::distance_symmetric<double> distance_strategy(1.0);  
    boost::geometry::strategy::buffer::end_round end_strategy(36);  
    boost::geometry::strategy::buffer::side_straight side_strategy;  
    boost::geometry::strategy::buffer::point_circle point_strategy;  
  
    MultiLinestring l1;  
    boost::geometry::read_wkt("MULTILINESTRING((11393 -19530,\  
    -5728 30983,-9223372036854775807 15322,27608 14504),\  
    (-32468 -20811,-27782 -14559,-30822 11140,-12550 27458),\  
    (24176 -9103,4178 -868,7332 11648,21439 30360,-121 11529,8803 -10835,5660 20213))", l1);  
  
    MultiPolygon result;  
  
    boost::geometry::buffer(l1, result,  
                            distance_strategy, side_strategy,  
                            join_strategy, end_strategy, point_strategy);  
  
    std::cout << boost::geometry::wkt(result) << std::endl;  
    std::cout << boost::geometry::is_valid(result) << std::endl;  
}  
```

---

## Comment 1

> Username: barendgehrels  
> Created at: 2024-01-27 10:47:37 UTC  
> Url: https://github.com/boostorg/geometry/issues/1233#issuecomment-1913112544  

This is probably an artificial testcase.  
We might detect the `-9223372036854775807` value, somehow.
