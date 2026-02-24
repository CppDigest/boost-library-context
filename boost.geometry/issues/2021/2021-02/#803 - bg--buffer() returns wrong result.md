# #803 - bg::buffer() returns wrong result [Closed]

> Username: Mitsuhiko-Matsukawa  
> Created at: 2021-02-05 15:30:42 UTC  
> Updated at: 2021-03-03 10:51:04 UTC  
> Closed at: 2021-03-03 10:51:03 UTC  
> Url: https://github.com/boostorg/geometry/issues/803  

In this code, bg::buffer() returns empty result.  
However if I change 6.6613381477509392e-16 to 0.0, it returns correct result.  
  
boost1.75.0, Visual Studio 2019  
  
```cpp  
#include <boost/geometry.hpp>  
#include <boost/geometry/geometries/point_xy.hpp>  
#include <boost/geometry/geometries/geometries.hpp>  
  
int main()  
{  
    typedef double coordinate_type;  
    typedef boost::geometry::model::d2::point_xy<coordinate_type> point;  
    typedef boost::geometry::model::polygon<point> polygon;  
  
    // Declare strategies  
    const double buffer_distance = 10.0;  
    const int points_per_circle = 36;  
    boost::geometry::strategy::buffer::distance_symmetric<coordinate_type> distance_strategy(buffer_distance);  
    boost::geometry::strategy::buffer::join_miter join_strategy(points_per_circle);  
    boost::geometry::strategy::buffer::end_round end_strategy(points_per_circle);  
    boost::geometry::strategy::buffer::point_circle circle_strategy(points_per_circle);  
    boost::geometry::strategy::buffer::side_straight side_strategy;  
  
    // Declare output  
    boost::geometry::model::multi_polygon<polygon> result;  
  
    // Declare/fill a linestring  
    boost::geometry::model::linestring<point> ls = {  
        { 2773.6899360413681, -17.493356405074767 },  
        { 2767.9084041267429, -9.0288291122330797 },  
        { 2765.7403296587586, -1.6929054585683376 },  
#if 1   // assertion failed (empty results)  
        { 2765.3273630934282, 6.6613381477509392e-16 },  
#else   // assertion successful  
        { 2765.3273630934282, 0.0 },  
#endif  
        { 2762.8495637014462, 10.157432751410026 },  
        { 2759.9587977441333, 24.829280059133669 },  
        { 2756.3453402974928, 38.372523727680374 },  
        { 2752.0091913615238, 45.708447381345117 }  
    };  
  
    // Create the buffer of a linestring  
    boost::geometry::buffer(ls, result,  
        distance_strategy, side_strategy,  
        join_strategy, end_strategy, circle_strategy);  
  
    assert(result.size() == 1);  
  
    return 0;  
}  
```

---

## Comment 1

> Username: barendgehrels  
> Created at: 2021-02-10 09:49:16 UTC  
> Url: https://github.com/boostorg/geometry/issues/803#issuecomment-776585135  

Hi @Mitsuhiko-Matsukawa , thanks for the report. I can reproduce it and will at it to our testsuite.  
  
Using the define `BOOST_GEOMETRY_NO_ROBUSTNESS` I get correct behavior (on Linux/clang). This will become the default in a future Boost version (hopefully 1.77). Internal behavior is then a bit different, it will not rescale, it might therefore  influence other cases.

---

## Comment 2

> Username: barendgehrels  
> Created at: 2021-02-10 10:13:51 UTC  
> Url: https://github.com/boostorg/geometry/issues/803#issuecomment-776600211  

By the way, this line is incorrect:  
`boost::geometry::strategy::buffer::join_miter join_strategy(points_per_circle);`  
because the miter strategy takes an optional `miter_limit` and not a number of points (that's not applicable there). It works but the miter limit is quite high. You can construct it without a parameter.  
  
It does not alter the test case.

---

## Comment 3

> Username: barendgehrels  
> Created at: 2021-02-19 13:29:48 UTC  
> Url: https://github.com/boostorg/geometry/issues/803#issuecomment-782076095  

I looked to it closer, when adding the testcase, and the root cause is actually an artifact generated in the buffer outline.  
That is fixed now in PR #815 and will be released in 1.76  
  
So there is no need to use that define, for this case.

---

## Comment 4

> Username: barendgehrels  
> Created at: 2021-03-03 10:51:03 UTC  
> Url: https://github.com/boostorg/geometry/issues/803#issuecomment-789623128  

Closing as fixed
