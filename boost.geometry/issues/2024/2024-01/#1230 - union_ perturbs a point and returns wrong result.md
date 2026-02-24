# #1230 - union_ perturbs a point and returns wrong result [Closed]

> Username: vissarion  
> Created at: 2024-01-26 12:05:59 UTC  
> Updated at: 2024-02-26 09:42:15 UTC  
> Closed at: 2024-02-26 09:42:15 UTC  
> Url: https://github.com/boostorg/geometry/issues/1230  

Boost geometry returns a wrong result by actually perturb slightly a point of the input that should appear in the output as is.   
  
In particular, the following program   
  
```cpp  
#include <boost/geometry.hpp>  
  
int main(){  
  
    using Point = boost::geometry::model::d2::point_xy<double>;  
    using Polygon = boost::geometry::model::polygon<Point>;  
  
    Polygon p1;  
    boost::geometry::read_wkt("POLYGON((5 0,15 25,25 0,15 5,5 0))", p1);  
    Polygon p2;  
    boost::geometry::read_wkt("POLYGON((25 0,0 15,30 15,22 10,25 0))", p2);  
    Polygon p3;  
    boost::geometry::read_wkt("POLYGON((5 0,7 10,0 15,10 15,15 25,20 15,30 15,22 10,25 0,15 5,5 0))", p3);  
  
    boost::geometry::model::multi_polygon<Polygon> result;  
    boost::geometry::union_(p1, p2, result);  
  
    boost::geometry::model::multi_polygon<Polygon> result2;  
    boost::geometry::union_(result, p3, result2);  
  
    std::cout << boost::geometry::wkt(result2) << std::endl;  
  
    return 0;  
}  
```  
  
returns `MULTIPOLYGON(((8.88178e-16 15,10 15,15 25,20 15,30 15,22 10,25 0,15 5,5 0,7 10,8.88178e-16 15)))` in boost 1.84 while returns the correct result `MULTIPOLYGON(((0 15,10 15,15 25,20 15,30 15,22 10,25 0,15 5,5 0,7 10,0 15)))` in 1.78.    
The perturbed point here is POINT(0 15).  
  
This is probably a regression of removing rescaling as a default in 1.79.
