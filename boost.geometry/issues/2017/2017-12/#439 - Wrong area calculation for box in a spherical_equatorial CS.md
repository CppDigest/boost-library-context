# #439 - Wrong area calculation for box in a spherical_equatorial CS [Closed]

> Username: daedric  
> Created at: 2017-12-11 15:38:09 UTC  
> Updated at: 2021-06-30 21:04:07 UTC  
> Closed at: 2021-06-30 21:04:07 UTC  
> Url: https://github.com/boostorg/geometry/issues/439  

Given the code (http://cpp.sh/43sl):  
  
```  
#include <iostream>  
#include <boost/geometry.hpp>  
  
using coordinate_system = boost::geometry::cs::spherical_equatorial<boost::geometry::degree>;  
using lonlat = boost::geometry::model::point<double, 2, coordinate_system>;  
using box = boost::geometry::model::box<lonlat>;  
using polygon = boost::geometry::model::polygon<lonlat>;  
  
int main()  
{  
    box b{  
        lonlat{0, 0},  
        lonlat{1, 1},  
    };  
  
    polygon p;  
    p.outer().push_back(lonlat{0, 0});  
    p.outer().push_back(lonlat{0, 1});  
    p.outer().push_back(lonlat{1, 1});  
    p.outer().push_back(lonlat{1, 0});  
    p.outer().push_back(lonlat{0, 0});  
  
    std::cout << wkt(b) << std::endl;  
    std::cout << wkt(p) << std::endl;  
    std::cout << area(b) << std::endl;  
    std::cout << area(p) << std::endl;  
}  
```  
  
It gives the following output:  
```  
POLYGON((0 0,0 1,1 1,1 0,0 0))  
POLYGON((0 0,0 1,1 1,1 0,0 0))  
1  
0.00030461  
```  
  
The problem is that boost::geometry calls the following code to calculate the are of the box:  
  
```struct box_area  
{  
    template <typename Box, typename Strategy>  
    static inline typename coordinate_type<Box>::type  
    apply(Box const& box, Strategy const&)  
    {  
        // Currently only works for 2D Cartesian boxes  
        assert_dimension<Box, 2>();  
  
        return (get<max_corner, 0>(box) - get<min_corner, 0>(box))  
             * (get<max_corner, 1>(box) - get<min_corner, 1>(box));  
    }  
};  
```  
  
Which as stated can't work in a spherical referential.  
Even giving the strategy explicitely does not workaround the bug :)  
  
Cheers,

---

## Comment 1

> Username: awulkiew  
> Created at: 2017-12-12 00:33:51 UTC  
> Url: https://github.com/boostorg/geometry/issues/439#issuecomment-350904992  

Thanks for report!  
  
Indeed the result is wrong however it should not be the same as the result for Polygon. In Boost.Geometry the Box in non-cartesian cordinate systems is not Polygon, i.e. its edges aren't defined by great circles or geodesics. Instead, the edges of a Box are defined by meridians and parallels. E.g. in spherical equatorial the result should be area of a part of spherical frustum/segment (don't confuse with a Boost.Geometry Segment on the surface of a sphere defined by great circle).
