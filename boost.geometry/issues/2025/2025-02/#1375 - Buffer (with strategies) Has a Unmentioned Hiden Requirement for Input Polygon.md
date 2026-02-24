# #1375 - Buffer (with strategies) Has a Unmentioned Hiden Requirement for Input Polygon [Open]

> Username: sssecs  
> Created at: 2025-02-17 07:54:36 UTC  
> Updated at: 2025-02-18 08:23:07 UTC  
> Url: https://github.com/boostorg/geometry/issues/1375  

Dear Boost Geometry Developers,  
  
First, I’d like to thank you for all the hard work you’ve put into Boost—it’s been invaluable to the community!  
  
I’m new to Boost Geometry and encountered an issue while trying to buffer a polygon. I was following the documentation (https://beta.boost.org/doc/libs/1_82_0/libs/geometry/doc/html/geometry/reference/algorithms/buffer/buffer_7_with_strategies.html), and surprisingly, the example polygon worked as expected, but the polygon I created didn’t.  
  
After some debugging and searching, I realized that the polygons in this related issue (https://github.com/boostorg/geometry/issues/1372) are defined as `using Polygon = bg::model::polygon<Point, false, false>`. However, the documentation defines the polygon type as `typedef boost::geometry::model::polygon<point> polygon`, which doesn’t specify the orientation of the vertices.  
  
To resolve the issue, I changed the polygon definition to `using polygon = boost::geometry::model::polygon<point, false>`, ensuring the polygon is defined as counter-clockwise. Alternatively, adjusting the vertex order of my input polygon to counter-clockwise also solved the problem.  
  
It would be very helpful if the buffer function could support both clockwise and counter-clockwise ordered polygons, or if this behavior could be clarified in the documentation. It would greatly assist newcomers like myself.  
  
Here’s my (non-working under 1.87 and 1.82) version of the code for reference:  
  
  
```  
#include <boost/geometry.hpp>  
#include <boost/geometry/algorithms/buffer.hpp>  
#include <boost/geometry/geometries/point_xy.hpp>  
#include <boost/geometry/geometries/geometries.hpp>  
#include <iostream>  
  
namespace bg = boost::geometry;  
  
int main()  
{  
    using coordinate_type = double;  
    using point = boost::geometry::model::d2::point_xy<coordinate_type>;  
    using polygon = boost::geometry::model::polygon<point, false>;  
  
    // Declare strategies  
    const double buffer_distance = 1.0;  
    const int points_per_circle = 16;  
    boost::geometry::strategy::buffer::distance_symmetric<coordinate_type> distance_strategy(buffer_distance);  
    boost::geometry::strategy::buffer::join_round join_strategy(points_per_circle);  
    boost::geometry::strategy::buffer::end_round end_strategy(points_per_circle);  
    boost::geometry::strategy::buffer::point_circle circle_strategy(points_per_circle);  
    boost::geometry::strategy::buffer::side_straight side_strategy;  
  
    // Declare output  
    boost::geometry::model::multi_polygon<polygon> result;  
  
    // Declare a polygon  
    polygon poly;  
    boost::geometry::read_wkt("POLYGON((77.1028 50.357,50 50,26.8513 7.1746,77.1028 50.357))", poly);  
  
    std::cout << "Poly: " << boost::geometry::wkt(poly) << std::endl;  
  
    // Create the buffer of a polygon  
    boost::geometry::buffer(poly, result,  
                            distance_strategy, side_strategy,  
                            join_strategy, end_strategy, circle_strategy);  
  
    std::cout << "Result: " << boost::geometry::wkt(result) << std::endl;  
  
    return 0;  
}  
```  
  
The result is:  
  
```  
Poly: POLYGON((77.1028 50.357,50 50,26.8513 7.1746,77.1028 50.357))  
Result: MULTIPOLYGON()  
```

---

## Comment 1

> Username: barendgehrels  
> Created at: 2025-02-17 17:39:31 UTC  
> Url: https://github.com/boostorg/geometry/issues/1375#issuecomment-2663767046  

Hi @sssecs , thanks for your report and your compliments.  
  
So, basically, a hyperlink is missing indeed. The buffer page says: "Any type fulfilling the Geometry Concept" and we could link to, for example, the "Polygon Concept" )https://beta.boost.org/doc/libs/1_82_0/libs/geometry/doc/html/geometry/reference/concepts/concept_polygon.html)  
  
where is explained that a polygon can be clockwise or counter clockwise and open or closed...

---

## Comment 2

> Username: barendgehrels  
> Created at: 2025-02-17 17:40:17 UTC  
> Url: https://github.com/boostorg/geometry/issues/1375#issuecomment-2663768382  

I'll keep this issue open to make it more clear in the documentation

---

## Comment 3

> Username: sssecs  
> Created at: 2025-02-18 02:47:02 UTC  
> Url: https://github.com/boostorg/geometry/issues/1375#issuecomment-2664484899  

Hi @barendgehrels,  
  
Thank you for your quick and insightful response!  
  
Your clarification of the polygon concept has resolved the confusion I had, as I was mistakenly associating it with the behavior in the Python Shapely library.  
  
I'm confident this issue will be useful for anyone else who might make the same mistake I did.
