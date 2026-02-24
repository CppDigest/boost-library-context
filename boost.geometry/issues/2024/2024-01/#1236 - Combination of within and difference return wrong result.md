# #1236 - Combination of within and difference return wrong result [Open]

> Username: vissarion  
> Created at: 2024-01-31 13:28:57 UTC  
> Updated at: 2024-01-31 13:29:23 UTC  
> Url: https://github.com/boostorg/geometry/issues/1236  

The following program returns `false` while it should return `true`.   
  
```cpp  
#include <boost/geometry.hpp>  
  
int main() {  
    using NT = double;  
    using Point = boost::geometry::model::d2::point_xy<NT>;  
    using Polygon = boost::geometry::model::polygon<Point>;  
    using MultiPolygon = boost::geometry::model::multi_polygon<Polygon>;  
      
    Polygon star_elem_horizontal; //blue  
    Polygon star_elem_vertical;   //yellow  
    Polygon star_1;               //red  
    Polygon star_2;               //green  
  
    boost::geometry::read_wkt("POLYGON((25 0,0 15,30 15,22 10,25 0))", star_elem_horizontal);  
    boost::geometry::read_wkt("POLYGON((5 0,15 25,25 0,15 5,5 0))", star_elem_vertical);  
    boost::geometry::read_wkt("POLYGON((5 0,7 10,0 15,10 15,15 25,20 15,30 15,22 10,25 0,15 5,5 0))",  
        star_1);  
    boost::geometry::read_wkt("POLYGON((15 0,17 10,10 15,20 15,25 25,30 15,40 15,32 10,35 0,25 5,15 0))",  
        star_2);  
  
    MultiPolygon mp1;  
    boost::geometry::difference(star_elem_horizontal, star_elem_vertical, mp1);  
  
    MultiPolygon mp2;  
    boost::geometry::difference(mp1, star_2, mp2);  
  
    MultiPolygon mp3;  
    boost::geometry::difference(star_1, star_2, mp3);  
  
    std::cout << boost::geometry::within(mp2, mp3) << std::endl;  
      
    return 0;  
}  
```  
  
It seems like a numerical precision issue. If we use `boost::multiprecision::number<boost::multiprecision::gmp_float<50> >` as `NT` then we get the desired output i.e. true.  
  
![Screenshot from 2024-01-26 15-14-47](https://github.com/boostorg/geometry/assets/3660366/67fc1f61-b8c7-4b8f-9451-76f6c302d8e9)
