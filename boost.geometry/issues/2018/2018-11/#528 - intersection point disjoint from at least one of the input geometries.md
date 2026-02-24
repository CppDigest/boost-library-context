# #528 - intersection point disjoint from at least one of the input geometries [Open]

> Username: maysl  
> Created at: 2018-11-14 12:34:15 UTC  
> Updated at: 2018-11-14 14:31:09 UTC  
> Url: https://github.com/boostorg/geometry/issues/528  

In this example, `intersection(polygon, linestring, multi_point)` returns an intersection point that is disjoint from the linestring.  
  
The linestring is de facto collinear with one of the polygon edges.  
  
Note that I had to use bitwise representation of the `double` coordinates to reproduce with a minimal example.  
  
I have observed other cases where the intersection is disjoint from both input geometries. It is however difficult to reproduce those because of (see above).  
  
``` cpp  
#include <array>  
#include <boost/geometry/algorithms/distance.hpp>  
#include <boost/geometry/algorithms/intersection.hpp>  
#include <boost/geometry/algorithms/is_valid.hpp>  
#include <boost/geometry/core/cs.hpp>  
#include <boost/geometry/geometries/linestring.hpp>  
#include <boost/geometry/geometries/multi_point.hpp>  
#include <boost/geometry/geometries/point.hpp>  
#include <boost/geometry/geometries/polygon.hpp>  
#include <boost/geometry/strategies/strategies.hpp>  
#include <cassert>  
  
using namespace boost::geometry;  
using Point = model::point<double, 2, cs::cartesian>;  
  
// NOTE: I had to use bitwise representation of the double coordinates  
//       to reproduce the issue with a minimal example. The geometries are  
//       generated programmatically in original code  
struct Bitwise_point {  
  std::uint64_t x_;  
  std::uint64_t y_;  
  
  operator Point() const {  
    static_assert(sizeof(double) == sizeof(std::uint64_t), "");  
    return {reinterpret_cast<double const&>(x_),  
            reinterpret_cast<double const&>(y_)};  
  }  
};  
  
std::array<Bitwise_point, 4> poly_points = {  
    {{13925016940604200307ull, 4702746055825940928ull},  // -2070817.172  2557246.237  
     {13687858170694336763ull, 4460898748575049558ull},  // -3.06186e-10  1.76777e-10  
     {13917655915177442577ull, 13914086191065865897ull}, // -702760.4419 -405738.9303  
     {13925016940604200307ull, 4702746055825940928ull}}};  
  
std::array<Bitwise_point, 2> segment_points = {  
    {{13924651584191793941ull, 4702520467028539693ull},    // -1.98575e+06  2.4522e+06  
     {13922451770662021208ull, 4700566379609457879ull}}};  // -1.47357e+06  1.8197e+06  
  
int main() {  
  model::polygon<Point> polygon;  
  polygon.outer().assign(poly_points.begin(), poly_points.end());  
  assert(is_valid(polygon));  
  
  model::linestring<Point> linestring(segment_points.begin(),  
                                      segment_points.end());  
  assert(is_valid(linestring));  
  
  model::multi_point<Point> intersection_result;  
  intersection(polygon, linestring, intersection_result);  
  
  auto intersection_point = intersection_result.at(0);  // -1.08943e+06  1.34533e+06  
  
  // the distance from either end of the (single-segment) linestring to the  
  // intersection point cannot be larger than the total length of the segment  
  auto linestring_length = distance(linestring[0], linestring[1]);  
  assert(distance(intersection_point, linestring[0]) <= linestring_length);  
  assert(distance(intersection_point, linestring[1]) <= linestring_length);  
}  
```
