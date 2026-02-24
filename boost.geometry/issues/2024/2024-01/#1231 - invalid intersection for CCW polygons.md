# #1231 - invalid intersection for CCW polygons [Closed]

> Username: vissarion  
> Created at: 2024-01-26 12:37:05 UTC  
> Updated at: 2024-04-01 10:22:05 UTC  
> Closed at: 2024-04-01 10:22:04 UTC  
> Url: https://github.com/boostorg/geometry/issues/1231  

The following program  
  
```cpp  
#include <boost/geometry.hpp>  
  
int main() {  
  
    using Point = boost::geometry::model::d2::point_xy<double>;  
    using MultiPoint = boost::geometry::model::multi_point<Point>;  
    using Polygon = boost::geometry::model::polygon<Point, false>;  
    using Linestring = boost::geometry::model::linestring<Point>;  
    using MultiPolygon = boost::geometry::model::multi_polygon<Polygon>;  
    using MultiLinestring = boost::geometry::model::multi_linestring<Linestring>;  
  
    Polygon p1, p2, p3;  
    boost::geometry::read_wkt("POLYGON((0 5,-6 -17,12 17,0 5),(4 6,5 5,0 1,4 6))", p1);  
    boost::geometry::read_wkt("POLYGON((3 9,-15 -5,13 -11,3 9))", p2);  
  
    MultiPolygon mp;  
  
    std::cout << boost::geometry::is_valid(p1) << std::endl;  
    std::cout << boost::geometry::is_valid(p2) << std::endl;  
  
  
    std::tuple<MultiPoint, MultiLinestring, MultiPolygon> result;  
    boost::geometry::intersection(p1, p2, result);  
  
    std::cout << boost::geometry::wkt(std::get<0>(result)) << std::endl;  
    std::cout << boost::geometry::wkt(std::get<1>(result)) << std::endl;  
    std::cout << boost::geometry::wkt(std::get<2>(result)) << std::endl;  
    std::cout << boost::geometry::area(std::get<2>(result)) << std::endl;  
  
    std::cout << boost::geometry::is_valid(std::get<2>(result)) << std::endl;  
  
    return 0;  
}  
```  
  
returns the invalid geometry   
`MULTIPOLYGON(((3.33333 8.33333,0 5,-3.40491 -7.48466,-1.21132 -7.95472,5.31429 4.37143,5 5,0 1,4 6,5 5,3.33333 8.33333)))`   
while for CW polygons ie. if we change line  
` using Polygon = boost::geometry::model::polygon<Point, false>;`  
above with   
` using Polygon = boost::geometry::model::polygon<Point, true>;`  
it returns   
`MULTIPOLYGON(((3.33333 8.33333,5 5,5.31429 4.37143,-1.21132 -7.95472,-3.40491 -7.48466,0 5,3.33333 8.33333),(5 5,4 6,0 1,5 5)))`  
which is valid and correct.  
  
This behavior is tested in 1.84 and also 1.78.   
However, in 1.77 the above code gives the correct valid result in all cases.  
  
The difference between the invalid and valid cases is that in the latter there is the creation of a hole `(5 5,4 6,0 1,5 5)` while in the former this is not true and the result is invalid. Notably the hole shares a point with the boundary of the output polygon.  
![Screenshot from 2024-01-26 14-32-23](https://github.com/boostorg/geometry/assets/3660366/d3f3be76-c228-45e2-a4b0-099e9e4a2ec6)

---

## Comment 1

> Username: barendgehrels  
> Created at: 2024-02-11 15:34:22 UTC  
> Url: https://github.com/boostorg/geometry/issues/1231#issuecomment-1937787203  

I can reproduce it.  
Output (in slightly modified test program):  
```  
70: issue_1231 : MULTIPOLYGON(((3.33333 8.33333,5 5,5.31429 4.37143,-1.21132 -7.95472,-3.40491 -7.48466,0 5,3.33333 8.33333),(5 5,4 6,0 1,5 5)))  
70: .../boost/libs/geometry/test/algorithms/set_operations/intersection/./test_intersection.hpp:120: error: in "test_main_caller( argc_ argv )": intersection: issue_1231 not valid: Geometry has invalid self-intersections. A self-intersection point was found at (5, 5); method: t; operations: i/i; segment IDs {source, multi, ring, segment}: {0, 0, -1, 4}/{0, 0, -1, 7} type: d ccw  
70: issue_1231 ccw : MULTIPOLYGON(((3.33333 8.33333,0 5,-3.40491 -7.48466,-1.21132 -7.95472,5.31429 4.37143,5 5,0 1,4 6,5 5,3.33333 8.33333)))  
70:   
```  
  
which is the same as you have. So it fails to generate an interior ring, while it should. The generation itself is OK.  
  
Also, we don't test `ccw` by default. If you turn it on, there are three errors in the intersection unit test, which might all be related. These are:  
```  
  
70: Test configuration:  
70:   - Debug mode  
70:   - No rescaling  
70:   - Testing only one type  
70:   - Default test type: d  
70:   
70: .../boost/libs/geometry/test/algorithms/set_operations/intersection/./test_intersection.hpp:120: error: in "test_main_caller( argc_ argv )": intersection: issue_1231 not valid: Geometry has invalid self-intersections. A self-intersection point was found at (5, 5); method: t; operations: i/i; segment IDs {source, multi, ring, segment}: {0, 0, -1, 4}/{0, 0, -1, 7} type: d ccw  
70: .../boost/libs/geometry/test/algorithms/set_operations/intersection/./test_intersection.hpp:120: error: in "test_main_caller( argc_ argv )": intersection: case_105 not valid: Geometry has invalid self-intersections. A self-intersection point was found at (5, 3); method: m; operations: u/u; segment IDs {source, multi, ring, segment}: {0, 0, 0, 0}/{0, 0, 1, 3} type: d ccw  
70: .../boost/libs/geometry/test/algorithms/set_operations/intersection/./test_intersection.hpp:120: error: in "test_main_caller( argc_ argv )": intersection: mysql_23023665_10 not valid: Geometry has invalid self-intersections. A self-intersection point was found at (5, 5); method: t; operations: i/i; segment IDs {source, multi, ring, segment}: {0, 0, -1, 4}/{0, 0, -1, 7} type: d ccw  
70: .../boost/libs/geometry/test/algorithms/set_operations/intersection/./test_intersection.hpp:120: error: in "test_main_caller( argc_ argv )": intersection: mysql_23023665_11 not valid: Geometry has invalid self-intersections. A self-intersection point was found at (3, -1); method: t; operations: i/i; segment IDs {source, multi, ring, segment}: {0, 0, -1, 0}/{0, 0, -1, 3} type: d ccw  
70:   
70: *** 4 failures are detected in the test module "Test Program"  
70:   
1/1 Test #70: boost_geometry_algorithms_intersection ...***Failed    0.71 sec  
```  
  
For the record, I added two lines, in `overlay_cases.hpp`  
```  
static std::string issue_1231[2] =  
{  
    "POLYGON((0 5,-6 -17,12 17,0 5),(4 6,5 5,0 1,4 6))",  
    "POLYGON((3 9,-15 -5,13 -11,3 9))"  
};  
  
```  
  
and in `intersection.cpp`  
```  
TEST_INTERSECTION(issue_1231, 1, -1, 54.701340543162516);  
```  
  
The output looks identical in both, and to your picture (here with `TEST_WITH_SVG`  
![image](https://github.com/boostorg/geometry/assets/334849/cca8436b-03c3-4741-bf92-bf6417722101)

---

## Comment 2

> Username: barendgehrels  
> Created at: 2024-03-03 16:05:32 UTC  
> Url: https://github.com/boostorg/geometry/issues/1231#issuecomment-1975207458  

It's difference is also wrong, both clockwise and counterclockwise.  
I'm making a PR for the testcase (not for the fix)

---

## Comment 3

> Username: vissarion  
> Created at: 2024-04-01 10:22:05 UTC  
> Url: https://github.com/boostorg/geometry/issues/1231#issuecomment-2029538572  

Fixed by https://github.com/boostorg/geometry/pull/1263
