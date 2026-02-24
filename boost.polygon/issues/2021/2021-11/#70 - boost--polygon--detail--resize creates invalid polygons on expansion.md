# #70 - boost::polygon::detail::resize creates invalid polygons on expansion [Open]

> Username: Elmi77  
> Created at: 2021-11-08 07:14:59 UTC  
> Updated at: 2024-01-15 10:22:49 UTC  
> Url: https://github.com/boostorg/polygon/issues/70  

I have a regular polygon that is expanded via functions in boost::polygon / boost::polygon::polygon_set_data resize().  
  
Expanding a polygon should result in a new outline (yellow) which nowhere touches the original polygon (green) and which nowhere comes closer to the original polygon outline than the expansion size:  
  
![clip_ok](https://user-images.githubusercontent.com/5111091/140699025-daecbb74-1593-4e9f-af95-0ee0c088cf0b.png)  
  
Unfortunately this is not the result I get with boost::polygon. Here the new ouline cuts the original one:  
  
![clip_boost](https://user-images.githubusercontent.com/5111091/140699079-af29c55b-5d23-4d24-9454-51a98c98b35a.png)  
  
In other words: the result is damaged and wrong even to its own specification. This happens with Boost 1.77.

---

## Comment 1

> Username: fililili  
> Created at: 2023-07-21 02:21:25 UTC  
> Url: https://github.com/boostorg/polygon/issues/70#issuecomment-1644895916  

can you give the polygon's data for test?

---

## Comment 2

> Username: dunanshan  
> Created at: 2024-01-15 10:22:48 UTC  
> Url: https://github.com/boostorg/polygon/issues/70#issuecomment-1891812046  

maybe polygon_set resize is ERROR always.  
When use boost::geometry::buffer, the result is right;  
but use polygon_set, the result is ERROR.  
right result: empty  
![geometryBufferRight](https://github.com/boostorg/polygon/assets/17263580/c2163eee-4e59-484b-9dbb-f121a565a23e)  
error result: exist an polygon outside   
![polygonResizeError](https://github.com/boostorg/polygon/assets/17263580/8836c788-69f4-48e7-9ea7-20aa6f71998d)  
  
all code is as follow:  
-----------------------------------  
#include <fstream>  
#include <boost/geometry.hpp>  
#include <boost/geometry/geometries/adapted/boost_polygon.hpp>  
#include <boost/polygon/polygon.hpp>  
#include <cassert>  
  
using namespace std;  
namespace gtl = boost::polygon;  
using namespace boost::polygon::operators;  
  
vector<int> srcData = {  
    688672, 1590035,  
    688438, 1592394,  
    688091, 1595907,  
    687859, 1598266,  
    687209, 1600398,  
    687545, 1597000,  
    687795, 1594459,  
    688032, 1592042,  
    687795, 1589625,  
    687545, 1587084,  
    687306, 1584666,  
    686722, 1582740,  
    686159, 1580883,  
    686159, 1578528,  
    686159, 1576209,  
    687170, 1579536,  
    687859, 1581805,  
    688091, 1584163,  
    688438, 1587676,  
    688672, 1590035,  
};  
  
auto sz = srcData.size() / 2;  
const int buffer_distance = -1000;  
  
//bufferTest  
int bufferTest()  
{  
    typedef int coordinate_type;  
    typedef boost::geometry::model::d2::point_xy<coordinate_type> point;  
    typedef boost::geometry::model::polygon<point> polygon;  
  
    polygon poly;  
    // CW Closed  
    for (int i = sz - 1; i >= 0; --i)  
        boost::geometry::append(poly.outer(), point(srcData[i * 2], srcData[i * 2 + 1]));  
  
    boost::geometry::model::multi_polygon<polygon> mpol;  
    mpol.emplace_back(move(poly));  
  
    // Declare strategies  
    const int points_per_circle = 36;  
    boost::geometry::strategy::buffer::distance_symmetric<coordinate_type> distance_strategy(buffer_distance);  
    boost::geometry::strategy::buffer::join_round join_strategy(points_per_circle);  
    boost::geometry::strategy::buffer::end_round end_strategy(points_per_circle);  
    boost::geometry::strategy::buffer::point_circle circle_strategy(points_per_circle);  
    boost::geometry::strategy::buffer::side_straight side_strategy;  
  
    // Declare output  
    boost::geometry::model::multi_polygon<polygon> result;  
  
    // Create the buffer of a multi polygon  
    boost::geometry::buffer(mpol, result,  
        distance_strategy, side_strategy,  
        join_strategy, end_strategy, circle_strategy);  
  
    return 0;  
}  
  
int offsetTest() {  
    //lets declare ourselves a polygon set  
    using namespace gtl; //because of operators  
    typedef boost::polygon::polygon_with_holes_data<int> polygon;  
    typedef boost::polygon::polygon_traits<polygon>::point_type point;  
    typedef boost::polygon::polygon_with_holes_traits<polygon>::hole_type hole;  
    typedef std::vector<polygon> polygonset;  
      
    vector<point> pts;  
    // CW closed  
    //for (int i = sz - 1; i >= 0; --i)  
    //    pts.emplace_back(point(srcData[i * 2], srcData[i * 2 + 1]));  
    // CCW open  
    for (int i = 0; i < sz - 1; ++i)  
        pts.emplace_back(point(srcData[i * 2], srcData[i * 2 + 1]));  
  
    polygon poly;  
    boost::polygon::set_points(poly, pts.begin(), pts.end());  
  
    polygonset mpol, result;  
    mpol.emplace_back(move(poly));  
  
    result = mpol + buffer_distance;  
  
    return 0;  
}  
  
int main() {  
      
    bufferTest();  
  
    // ERROR!!!  
    offsetTest();  
      
    return 0;  
}  
----------------------------
