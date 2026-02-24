# #1433 - Buffering Linestrings with one overlapping point and segment removes hole [Open]

> Username: tschnz  
> Created at: 2025-10-03 16:16:41 UTC  
> Updated at: 2025-12-14 10:14:50 UTC  
> Url: https://github.com/boostorg/geometry/issues/1433  

I try to fake a closed Linestring by repeating the first two points so that the true first and last point are perfectly connected by a join.  I figured that may lead to problems so I did some tests where I throw a unique, correct and simplify on the linestring before buffering it.  
  
However I try it, I run into a lot of cases where the buffered Linestring collapses into one big Polygon without hole. It's happening with 1.87 and 1.88 but I couldn't make 1.89 work yet (vcpkg port yet missing).  
  
Btw this mostly happens with the following pipeline:  
Polygon -> Buffer (Miter) -> outer ring to Linestring -> append 2nd point of outer ring to linestring to close -> Buffer (again Miter) -> resulting Multipolygon collapses  
  
If I change the offset to 0.0025 it works but I can recreate a lot of these collapsed polygons with random values.  
  
https://godbolt.org/z/zEsxY9Wa5 (only supports 1.87):  
```  
#include <boost/geometry.hpp>  
#include <boost/geometry/geometries/point_xy.hpp>  
#include <boost/geometry/geometries/polygon.hpp>  
#include <boost/geometry/multi/geometries/multi_polygon.hpp>  
#include <boost/geometry/strategies/buffer.hpp>  
#include <iostream>  
  
namespace bg = boost::geometry;  
  
using Point    = bg::model::d2::point_xy<double>;  
using Linestring = bg::model::linestring<Point>;  
using Polygon  = bg::model::polygon<Point, true, true>;  
using MPolygon = bg::model::multi_polygon<Polygon>;  
  
int main() {  
  
    std::cout << "Using Boost "  
        << BOOST_VERSION / 100000 << "."  // maj. version  
        << BOOST_VERSION / 100 % 1000 << "."  // min. version  
        << BOOST_VERSION % 100                // patch version  
        << std::endl;  
  
    double offset = 0.0024999999441206455;  
    const bg::strategy::buffer::distance_symmetric<double> distance_strategy(offset);  
    const bg::strategy::buffer::join_miter            join_strategy(1.0);  
    static const bg::strategy::buffer::end_flat              end_strategy;  
    static const bg::strategy::buffer::side_straight         side_strategy;  
    static const bg::strategy::buffer::point_square          point_strategy;  
  
    const Linestring lstring_source = bg::from_wkt<Linestring>("LINESTRING(1.0819919262613131 1.8488013725600743,4.1041533228042875 -0.87236450916645436,-2.8075663587545185 -3.1181184584717836,-1.2965867878487485 3.990482902620351,1.7255742852176501 1.2693173121533894)");  
    MPolygon mpoly_out;  
    bg::buffer(lstring_source, mpoly_out, distance_strategy, side_strategy, join_strategy, end_strategy,  
               point_strategy);  
  
    // LINESTRING  
    std::cout << "-> ORIGINAL\n";  
    std::cout << bg::wkt(lstring_source) << "\n";  
    std::cout << lstring_source.size() << " points\n\n";  
      
    // BUFFERED MULTIPOLYGON  
    std::cout << "-> BUFFERED\n";  
    size_t num_points_total = 0;  
    for(auto const& po: mpoly_out)  
    {      
        std::cout << bg::wkt(po) << "\n";  
        std::cout << "Holes: " << po.inners().size() << std::endl;  
        num_points_total += po.outer().size();  
        for(const auto& pi: po.inners())  
        {  
            num_points_total += pi.size();  
        }  
    }  
    std::cout << num_points_total << " total points\n\n";  
  
    return 0;  
}  
```
