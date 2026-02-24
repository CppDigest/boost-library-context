# #1235 - intersection returns an extra point in the output [Open]

> Username: vissarion  
> Created at: 2024-01-29 10:09:47 UTC  
> Updated at: 2024-09-12 06:40:52 UTC  
> Url: https://github.com/boostorg/geometry/issues/1235  

The following program returns an extra point in 1.84 and develop while that was not the case in 1.77  
  
```cpp  
#include <boost/geometry.hpp>  
#include <iostream>  
  
int main() {  
    using Point = boost::geometry::model::d2::point_xy<double>;  
    using MultiPoint = boost::geometry::model::multi_point<Point>;  
    using Polygon = boost::geometry::model::polygon<Point, false>;  
    using Linestring = boost::geometry::model::linestring<Point>;  
    using MultiPolygon = boost::geometry::model::multi_polygon<Polygon>;  
    using MultiLinestring = boost::geometry::model::multi_linestring<Linestring>;  
  
    Polygon p;  
    boost::geometry::read_wkt("POLYGON((6 7,18 14,-8 1,0 0,18 -8,6 7),(6 0,-4 3,5 3,6 0))", p);  
  
    MultiPolygon mp;  
    boost::geometry::read_wkt("MULTIPOLYGON(((2 3,-3 5,-10 -1,2 3)))", mp);  
  
    std::tuple<MultiPoint, MultiLinestring, MultiPolygon> result;  
    boost::geometry::intersection(p, mp, result);  
  
    std::cout << boost::geometry::wkt(std::get<0>(result)) << std::endl;  
    std::cout << boost::geometry::wkt(std::get<1>(result)) << std::endl;  
    std::cout << boost::geometry::wkt(std::get<2>(result)) << std::endl;  
  
    std::cout << boost::geometry::is_valid(p) << std::endl;  
    std::cout << boost::geometry::is_valid(mp) << std::endl;  
    std::cout << boost::geometry::is_valid(std::get<2>(result)) << std::endl;  
}  
```  
  
In particular:  
  
In 1.84  
```  
MULTIPOINT((-7.2 1.4))  
MULTILINESTRING()  
MULTIPOLYGON(((-5.09091 0.636364,-0.842105 2.05263,-4 3,-7.2 1.4,-7.70909 0.963636,-5.09091 0.636364)),((-1.33333 4.33333,-4 3,2 3,-1.33333 4.33333)))  
```  
In 1.77  
```  
MULTIPOINT()  
MULTILINESTRING()  
MULTIPOLYGON(((-5.09091 0.636364,-0.842105 2.05263,-4 3,-7.2 1.4,-7.70909 0.963636,-5.09091 0.636364)),((-1.33333 4.33333,-4 3,2 3,-1.33333 4.33333)))  
```
