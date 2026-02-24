# #874 - Regression for intersection of box and polygon [Closed]

> Username: ghost  
> Created at: 2021-06-30 15:06:31 UTC  
> Updated at: 2021-09-02 13:37:20 UTC  
> Closed at: 2021-09-02 13:37:20 UTC  
> Url: https://github.com/boostorg/geometry/issues/874  

Dear maintainers of Boost Geometry,  
  
there appears to be another issue that is potentially related to #861 and #864 and that appears to be a regression that was introduced with boost 1.76.0.  
  
The program  
  
``` C++  
#include <iostream>  
#include <boost/geometry.hpp>  
#include <boost/geometry/core/coordinate_system.hpp>  
#include <boost/geometry/geometries/point_xy.hpp>  
  
namespace bg = boost::geometry;  
namespace bm = bg::model::d2;  
namespace srs = bg::srs;  
  
using Pt = bm::point_xy<double, bg::cs::geographic<bg::degree> >;  
using Box = bg::model::box<Pt>;  
using LineString = bg::model::linestring<Pt>;  
using Polygon = bg::model::polygon<Pt>;  
using MultiPolygon = bg::model::multi_polygon<Polygon>;  
  
int main() {  
  Polygon p;  
  Box b;  
  bg::read_wkt ("POLYGON((9.18 48.78, 9.08 48.78, 13.3 52.52, 13.4 52.52, 9.18 48.78))", p);  
  bg::read_wkt ("BOX(11.71142 51.196289,11.733398 51.218261)", b);  
  
  MultiPolygon result;  
  bg::intersection (p, b, result);  
  
  std::cout << bg::wkt (result);  
  return 0;  
}  
```  
  
yields a correctly cropped smaller polygon for boost version 1.75.0.  
  
However, boost version 1.76.0 incorrectly returns the original input polygon `p` as a result.  
  
- program with boost 1.75.0 (plausible output): https://godbolt.org/z/GYbPzWdPG  
- program with boost 1.76.0 (incorrect output): https://godbolt.org/z/PPcczjvY9  
  
Kind regards

---

## Comment 1

> Username: vissarion  
> Created at: 2021-09-02 10:45:08 UTC  
> Url: https://github.com/boostorg/geometry/issues/874#issuecomment-911524470  

In `1.77.0` boost geometry returns the same more plausible result as in `1.75.0`. So this could be some issue (probably regarding turns computation) that appeared in `1.76.0` and fixed in `1.77.0`.  
  
> However, boost version 1.76.0 incorrectly returns the original input polygon p as a result.  
  
Actually it also contains one more point.
