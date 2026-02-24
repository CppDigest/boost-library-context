# #1232 - Wrong result in difference of linestrings [Open]

> Username: vissarion  
> Created at: 2024-01-26 12:57:57 UTC  
> Updated at: 2024-01-26 13:01:20 UTC  
> Url: https://github.com/boostorg/geometry/issues/1232  

The program  
  
```cpp  
#include <boost/geometry.hpp>  
  
int main() {  
  
  
    using Point = boost::geometry::model::d2::point_xy<double>;  
    using Polygon = boost::geometry::model::polygon<Point>;  
    using Linestring = boost::geometry::model::linestring<Point>;  
    using MultiPolygon = boost::geometry::model::multi_polygon<Polygon>;  
    using MultiLinestring = boost::geometry::model::multi_linestring<Linestring>;  
  
    Linestring l1;  
    boost::geometry::read_wkt("LINESTRING(5 -8,-7 -6,-3 6,-3 1,-5 4,-1 0,8 5,5 1,-2 3, 1 10,8 5,6 2,7 4)", l1);  
  
    MultiLinestring ml1, ml2;  
    boost::geometry::read_wkt("MULTILINESTRING((1.9375 1.875,1.7441860465116283 1.9302325581395348,-0.7692307692307692 2.6483516483516487,-2 3,-1.0071942446043165 5.316546762589928))", ml1);  
  
    boost::geometry::difference(l1, ml1, ml2);  
  
    std::cout << boost::geometry::wkt(ml2) << std::endl;  
  
    std::cout << boost::geometry::is_valid(l1) << std::endl;  
    std::cout << boost::geometry::is_valid(ml1) << std::endl;  
    std::cout << boost::geometry::is_valid(ml2) << std::endl;  
  
    std::cout << boost::geometry::length(l1) << std::endl;  
    std::cout << boost::geometry::length(ml1) << std::endl;  
    std::cout << boost::geometry::length(ml2) << std::endl;  
  
}  
```  
  
returns   
```  
MULTILINESTRING((5 -8,-7 -6,-3 6,-3 1,-5 4,-1 0,8 5,5 1,1.9375 1.875))  
1  
1  
1  
83.7125  
6.61539  
57.5577  
```  
in boost 1.84, which is wrong.  
  
In boost 1.77 it throws an Inconsistent Turns exception.  
  
![Screenshot from 2024-01-26 14-50-17](https://github.com/boostorg/geometry/assets/3660366/a3975f2e-12f2-4c5b-b8eb-f00861c0af2e)  
in figure `ml1` is red, `ml2` is green and the union of red-green-purple is `l1`
