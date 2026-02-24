# #1000 - The function boost::geometry::line_interpolate provides wrong results. [Closed]

> Username: jimmyshe  
> Created at: 2022-05-16 06:35:02 UTC  
> Updated at: 2022-05-23 09:49:21 UTC  
> Closed at: 2022-05-23 09:49:21 UTC  
> Url: https://github.com/boostorg/geometry/issues/1000  

What I found is that , when the input geometry type is not exactly match with the output point like type, this function can not provide correct results for some input.  It works for certain data but not all.  
  
Here is the code for reproducing the bug.  
  
I tested the code using both boost 1.71 and 1.77 on ubuntu20.04 gcc 9.4.0. and clang10.0.0. All the combinations give me the same result.  
  
``` c++  
  
  
#include <boost/fusion/include/adapt_struct_named.hpp>  
#include <boost/geometry.hpp>  
#include <boost/geometry/geometries/adapted/boost_fusion.hpp>  
#include <iostream>  
#include <iomanip>  
struct Point {  
    double x;  
    double y;  
    Point() = default;  
    Point(double x, double y)  
        : x(x), y(y)  
    {  
    }  
};  
  
BOOST_FUSION_ADAPT_STRUCT(Point, (double, x)(double, y))  
BOOST_GEOMETRY_REGISTER_BOOST_FUSION_CS(cs::cartesian)  
using Line = boost::geometry::model::linestring<Point>;  
  
int main()  
{  
    using point_type = boost::geometry::model::d2::point_xy<double>;  
    using linestring_type = boost::geometry::model::linestring<point_type>;  
    Line l1;  
  
    boost::geometry::read_wkt("LINESTRING("  
                              "13534187.97 4710766.12,"  
                              "13534197.43 4710790.00,"  
                              "13534224.15 4710830.21,"  
                              "13534230.07 4710836.83,"  
                              "13534259.87 4710864.74,"  
                              "13534308.82 4710902.11)",  
                              l1);  
  
    double s = 10.0;  
  
    std::cout << std::fixed << std::setprecision(2);  
  
    Point p1;  
    boost::geometry::line_interpolate(l1, s, p1);  
    std::cout << "p1: " << boost::geometry::wkt(p1) << std::endl;  
  
    point_type p2;  
    boost::geometry::line_interpolate(l1, s, p2);  
    std::cout << "p2: " << boost::geometry::wkt(p2) << std::endl;  
  
  
  
    return 0;  
}  
  
```  
  
Out put of the code:  
```  
p1: POINT(13534191.65 4710775.42)  
p2: POINT(13534304.64 4710898.92)  
```  
p1 and p2 should be the same.
