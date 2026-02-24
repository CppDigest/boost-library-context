# #935 - return_envelope() gives wrong box for small value and cs::geographic [Closed]

> Username: victl  
> Created at: 2021-11-09 17:36:26 UTC  
> Updated at: 2021-12-16 18:00:38 UTC  
> Closed at: 2021-12-16 14:02:31 UTC  
> Url: https://github.com/boostorg/geometry/issues/935  

I'm using `boost::geometry::cs::geographic<boost::geometry::radian>` as `CoordinateSystem`, here is the code to reproduce:  
```  
#include <boost/geometry.hpp>  
#include <iostream>  
  
using coordinate_system = boost::geometry::cs::geographic<boost::geometry::radian>;  
using lonlat = boost::geometry::model::point<double, 2, coordinate_system>;  
using box = boost::geometry::model::box<lonlat>;  
using polygon = boost::geometry::model::polygon<lonlat>;  
using linestring = boost::geometry::model::linestring<lonlat>;  
  
int main()  
{  
    // This is basically a vertical line near WGS84 origin (0.0, 0.0)  
    linestring l{  
            {4.5055430885891123e-05, -2.7518149670422367e-06},  
            {4.5055130529987143e-05, 8.2400127103300943e-07},  
            {4.5054830174083163e-05, 4.3998175091082556e-06},  
    };  
  
    box mbr1 = boost::geometry::return_envelope<box>(l);  
  
    std::cout << std::setprecision(21) << std::fixed;  
    std::cout << boost::geometry::wkt(mbr1.min_corner()) << " " << boost::geometry::wkt(mbr1.max_corner()) << std::endl;  
    return 0;  
}  
```  
  
With `boost 1.76.0`, I got this result:  
```  
POINT(0.000045054830174083163 0.000000824001271033009) POINT(0.000045055430885891123 0.000004399817509108256)  
```  
  
As you can see, the `min_corner.y()` is `8.2400127103300943e-07`, instead of `-2.7518149670422367e-06`  
  
many thanks to the example code of https://github.com/boostorg/geometry/issues/471

---

## Comment 1

> Username: victl  
> Created at: 2021-12-16 18:00:38 UTC  
> Url: https://github.com/boostorg/geometry/issues/935#issuecomment-996051220  

Thank you!
