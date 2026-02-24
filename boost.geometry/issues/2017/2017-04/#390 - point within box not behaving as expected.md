# #390 - point within box not behaving as expected [Closed]

> Username: agauniyal  
> Created at: 2017-04-04 14:35:45 UTC  
> Updated at: 2017-04-04 16:21:45 UTC  
> Closed at: 2017-04-04 16:21:45 UTC  
> Url: https://github.com/boostorg/geometry/issues/390  

Pardon me if I'm wrong, but why would such an example give result as '_no_'  
  
```cpp  
#include <iostream>  
#include <boost/geometry.hpp>  
#include <boost/geometry/geometries/register/point.hpp>  
#include <boost/geometry/geometries/register/box.hpp>  
  
struct my_point  
{  
    double x, y;  
	my_point(double _x, double _y) : x(_x), y(_y) {}  
};  
  
struct my_box  
{  
    my_point ll, ur;  
    my_box(double x1, double y1, double x2, double y2) : ll(x1,y1), ur(x2,y2) {}  
};  
  
// Register the point type  
BOOST_GEOMETRY_REGISTER_POINT_2D(my_point, double, cs::cartesian, x, y)  
  
// Register the box type, also notifying that it is based on "my_point"  
BOOST_GEOMETRY_REGISTER_BOX(my_box, my_point, ll, ur)  
  
int main()  
{  
    my_box b(2,1,3,4);  
    my_point p(2.0,2.0);  
	std::cout << "within: " << (boost::geometry::within(p, b) ? "yes" : "no") << std::endl;  
    return 0;  
}  
```  
  
I tried to draw a rectangle on paper and the point fell inside it. I'm sure there is something wrong with my approach here, is that `box b` not qualified to be a box? Or is it something else?

---

## Comment 1

> Username: awulkiew  
> Created at: 2017-04-04 14:45:56 UTC  
> Updated at: 2017-04-04 14:47:01 UTC  
> Url: https://github.com/boostorg/geometry/issues/390#issuecomment-291522916  

This is how `within` predicate is defined [1][2], `within` returns `true` only if the first geometry overlaps the interior of the second geometry. Use `covered_by` instead.  
  
[1] https://en.wikipedia.org/wiki/DE-9IM  
[2] http://www.opengeospatial.org/standards/sfa

---

## Comment 2

> Username: agauniyal  
> Created at: 2017-04-04 16:21:45 UTC  
> Url: https://github.com/boostorg/geometry/issues/390#issuecomment-291553673  

seems about right!
