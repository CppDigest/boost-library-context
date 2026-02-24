# #858 - boost::geometry::intersection provides inconstent results for integers [Open]

> Username: jmmaljaars  
> Created at: 2021-05-28 09:53:28 UTC  
> Updated at: 2021-05-28 09:53:28 UTC  
> Url: https://github.com/boostorg/geometry/issues/858  

Consider the case in which two connected, but non-overlapping polygons with integer valued vertices are intersected by a horizontal line, e.g.  
  
```  
 o----o---------o  
 |  A  \   B    |  
 |      \       |  
-------------------- intersecting line L  
 |        \     |  
 0--------o-----o  
```  
  
Obviously, the end-point of the intersection between polygon `A` and the line `L` should match the starting point of the intersection between polygon `B` and the intersecting line `L`. This however fails for the following MWE for certain y-coordinates, i.e.   
  
```  
#include <vector>  
#include <iostream>  
  
#include <boost/geometry.hpp>  
#include <boost/geometry/geometries/register/ring.hpp>  
#include <boost/geometry/geometries/register/point.hpp>  
  
struct Point  
{  
    Point() : x(0), y(0) {}  
    Point(int x, int y) : x(x), y(y) {}  
    int x;  
    int y;  
};  
  
/**  
 * Registereing the 2D Point as int gives  
 * incorrect intersections  
*/  
BOOST_GEOMETRY_REGISTER_POINT_2D(Point, int,  
                                 cs::cartesian, x, y)  
BOOST_GEOMETRY_REGISTER_RING(std::vector<Point>)  
  
using Line = boost::geometry::model::linestring<Point>;  
  
int main(int argc, char **argv)  
{  
    /**  
     * Create two connected facets, to check whether the  
     * to check whether the calculated facet-line intersections match across facet boundaries  
     *  
     *  1----2---------5  
     *  |     \        |  
     *  |poly1 \ poly2 |  
     *  |       \      |  
     *  0 -------3-----4  
    */  
  
    const Point p0 = Point(0, 0);  
    const Point p1 = Point(0, 7);  
    const Point p2 = Point(7, 9);  
    const Point p3 = Point(9, 0);  
    const Point p4 = Point(16, 0);  
    const Point p5 = Point(16, 7);  
  
    std::vector<Point> poly1{p0, p1, p2, p3};  
    std::vector<Point> poly2{p2, p5, p4, p3};  
  
    // Make sure orientations are correct  
    boost::geometry::correct(poly1);  
    boost::geometry::correct(poly2);  
  
    for (int y = 0; y != 8; y++)  
    {  
        std::cout << "y-coordinate " << y << std::endl;  
        std::vector<Line> intersections1;  
        std::vector<Line> intersections2;  
  
        Line l;  
        l.push_back(Point(-1, y));  
        l.push_back(Point(17, y));  
  
        // Calculate intersection between l and (poly1, poly2)  
        boost::geometry::intersection(poly1, l, intersections1);  
        boost::geometry::intersection(poly2, l, intersections2);  
  
        std::cout << "Line intersection l - poly1: " << boost::geometry::wkt(intersections1[0]) << std::endl;  
        std::cout << "Line intersection l - poly2: " << boost::geometry::wkt(intersections2[0]) << std::endl;  
    }  
}  
```  
  
Returns for y-coordinate 6:  
  
```  
y-coordinate 6  
Line intersection l - poly1: LINESTRING(0 6,7 6)  
Line intersection l - poly2: LINESTRING(8 6,16 6)  
```  
  
Thus clearly leaving "a gap" between `poly1` and `poly2`  
  
However, changing the macro for registering a 2d point to `float`, i.e.  
  
```  
BOOST_GEOMETRY_REGISTER_POINT_2D(Point, float, cs::cartesian, x, y)  
```  
gives the expected result  
```  
y-coordinate 6  
Line intersection l - poly1: LINESTRING(0 6,7 6)  
Line intersection l - poly2: LINESTRING(7 6,16 6)  
```  
  
Am I missing something here? Or is this a bug in the `boost::geometry::intersection` algorithm for `int` values?  
(BTW: I am using boost 1.71 on ubuntu 20.04.)
