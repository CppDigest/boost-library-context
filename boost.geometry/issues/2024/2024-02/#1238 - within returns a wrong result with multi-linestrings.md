# #1238 - within returns a wrong result with multi-linestrings [Open]

> Username: vissarion  
> Created at: 2024-02-05 15:57:51 UTC  
> Updated at: 2024-02-05 15:57:51 UTC  
> Url: https://github.com/boostorg/geometry/issues/1238  

The following program returns true while this is clearly not the case.  
  
```cpp  
#include <boost/geometry.hpp>  
  
int main() {  
    using Point = boost::geometry::model::d2::point_xy<double>;  
    using Linestring = boost::geometry::model::linestring<Point>;  
    using MultiLinestring = boost::geometry::model::multi_linestring<Linestring>;  
  
    MultiLinestring ml1, ml2;  
    boost::geometry::read_wkt("MULTILINESTRING((10 10, 20 20 ), (15 15 , 30 15))", ml1);  
    boost::geometry::read_wkt("MULTILINESTRING((10 10, 20 20), (60 -60, 30 15))", ml2);  
  
    std::cout << boost::geometry::within(ml2, ml1) << std::endl;  
  
    return 0;  
}  
```
