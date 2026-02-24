# #484 - Haversine distance does not always multiply by radius [Open]

> Username: daedric  
> Created at: 2018-05-16 14:49:47 UTC  
> Updated at: 2018-05-17 10:21:59 UTC  
> Url: https://github.com/boostorg/geometry/issues/484  

Hello,  
I might have found an issue in the point to polygon distance calculation.  
  
Given this code:  
  
```  
#include <iostream>  
#include <boost/geometry.hpp>  
#include <boost/geometry/geometries/multi_polygon.hpp>  
#include <boost/geometry/geometries/polygon.hpp>  
#include <boost/geometry/strategies/spherical/distance_haversine.hpp>  
  
using coordinate_system = boost::geometry::cs::spherical_equatorial<boost::geometry::degree>;  
using lonlat = boost::geometry::model::point<double, 2, coordinate_system>;  
using polygon = boost::geometry::model::polygon<lonlat, false>;  
using multi_polygon = boost::geometry::model::multi_polygon<polygon>;  
static const constexpr double mean_radius = 6371.0 * 1000;  
static const auto Haversine = [] {  
    return boost::geometry::strategy::distance::haversine<double>(  
        mean_radius);  
};  
  
int main()  
{  
    std::string c = "POINT(2.37314 48.854)";  
    std::string p = "MULTIPOLYGON(((2.37198 48.8539, 2.37214 48.8539, 2.3721 48.854, 2.37198 48.854, 2.37198 48.8539)))";  
      
    lonlat center;  
    multi_polygon poly;  
    boost::geometry::read_wkt(c, center);  
    boost::geometry::read_wkt(p, poly);  
      
    {  
        int i = 0;  
        for (auto p : poly[0].outer())  
        {  
            auto distance = boost::geometry::distance(center, p, Haversine());  
            std::cout << "Haversine distance center<>polygons[0][" << i++ << "]: " << distance << std::endl;  
        }  
    }  
      
    {  
        auto distance = boost::geometry::distance(center, poly, Haversine());  
        std::cout << "Haversine distance center<>polygons: " << distance << std::endl;  
        std::cout << "Haversine distance center<>polygons * radius: " << distance*mean_radius << std::endl;  
    }  
}  
```  
  
The output with boost 1.67 is:  
  
```  
Haversine distance center<>polygons[0][0]: 85.5957  
Haversine distance center<>polygons[0][1]: 74.0043  
Haversine distance center<>polygons[0][2]: 76.0906  
Haversine distance center<>polygons[0][3]: 84.8703  
Haversine distance center<>polygons[0][4]: 85.5957  
Haversine distance center<>polygons: 1.16158e-05  
Haversine distance center<>polygons * radius: 74.0043  
```  
  
As one can see, the distance returned does not seem to be multiplied by the given radius.  
  
~This leads to another question, it is not clear in the documentation that the distance between a point and a polygon is the distance between the point and the closest point of the polygon.~  
  
Cheers :)

---

## Comment 1

> Username: daedric  
> Created at: 2018-05-16 15:29:00 UTC  
> Updated at: 2018-05-16 15:29:12 UTC  
> Url: https://github.com/boostorg/geometry/issues/484#issuecomment-389561548  

The bug seems to be caused by some fallback on the strategy::distance::cross_track to which the Haversine strategy is not given so it is instantiated with the default radius of 1.

---

## Comment 2

> Username: vissarion  
> Created at: 2018-05-17 10:21:58 UTC  
> Url: https://github.com/boostorg/geometry/issues/484#issuecomment-389819489  

First of all, when computing the distance between point and polygon the correct strategy to use is strategy::distance::cross_track and not strategy::distance::haversine. Then you will get the desired result.   
  
On the other hand the fallback call that you mention is indeed buggy since it neglects the radius of the passed haversine strategy.
