# #1033 - clang/VS warning about double to int conversion in geometry\algorithms\area.hpp [Closed]

> Username: wanghan02  
> Created at: 2022-07-08 10:18:14 UTC  
> Updated at: 2023-08-28 13:52:26 UTC  
> Closed at: 2022-07-11 18:29:37 UTC  
> Url: https://github.com/boostorg/geometry/issues/1033  

Simple code below with boost 1.79.0 triggers warning about double to int conversion.  
  
Visual Studio with Warning LEVEL4 (/W4):  
`boost_1_79_0\boost\geometry\algorithms\area.hpp(78,42): warning C4244: 'return': conversion from 'return_type' to 'int', possible loss of data`  
  
clang with -Wfloat-conversion:  
`boost_1_79_0/boost/geometry/algorithms/area.hpp:78:16: warning: implicit conversion turns floating-point number into integer: 'double' to 'typename coordinate_type<box<point<int, 2, cartesian> > >::type' (aka 'int') [-Wfloat-conversion]`  
```  
#include <boost/geometry.hpp>  
  
int main()  
{  
    typedef boost::geometry::model::point<int, 2, boost::geometry::cs::cartesian> point_t;  
    typedef boost::geometry::model::polygon<point_t> polygon_t;  
    typedef boost::geometry::model::box<point_t> box_t;  
      
  
    polygon_t polygon{{{0, 0}, {0, 5}, {5, 5}}};  
  
    box_t box(point_t(0, 0), point_t(5, 5));   
  
    boost::geometry::model::multi_polygon<polygon_t> polygonOut;  
  
    boost::geometry::difference(polygon, box, polygonOut);  
  
    return 0;  
}  
```

---

## Comment 1

> Username: awulkiew  
> Created at: 2022-07-11 18:29:56 UTC  
> Url: https://github.com/boostorg/geometry/issues/1033#issuecomment-1180731406  

Thanks.

---

## Comment 2

> Username: wanghan02  
> Created at: 2023-08-28 13:52:26 UTC  
> Url: https://github.com/boostorg/geometry/issues/1033#issuecomment-1695741355  

I can confirm that the problem is gone with boost 1.83.0.
