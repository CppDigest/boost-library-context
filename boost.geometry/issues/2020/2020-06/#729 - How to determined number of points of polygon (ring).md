# #729 - How to determined number of points of polygon (ring)? [Closed]

> Username: 0xtrzy  
> Created at: 2020-06-18 13:48:40 UTC  
> Updated at: 2020-06-18 21:19:15 UTC  
> Closed at: 2020-06-18 18:55:19 UTC  
> Url: https://github.com/boostorg/geometry/issues/729  

```  
#include <iostream>  
#include <deque>  
  
#include <boost/geometry.hpp>  
#include <boost/geometry/geometries/point_xy.hpp>  
#include <boost/geometry/geometries/polygon.hpp>  
  
#include <boost/foreach.hpp>  
  
typedef boost::geometry::model::d2::point_xy<double> point_t;  
typedef boost::geometry::model::polygon<point_t> polygon_t;  
  
int main()  
{  
polygon_t green;  
point_t   a;  
int       j = 0;  
  
 BOOST_GEOMETRY_REGISTER_MULTI_POLYGON(std::deque<polygon_t>);  
  
 boost::geometry::read_wkt("POLYGON((0 4, 3 1, 6 4, 3 0)(0 3, 0.1 3, 0.1 3.5, 0 3.5))", green);  
  
 std::cout << "green: segment=" << boost::geometry::num_segments(green) << " point=" << boost::geometry::num_points(green) << "]: " << std::endl;  
 std::cout << "valid? " << (boost::geometry::is_valid(green) ? "yes" : "no") << std::endl;  
  
 std::deque<point_t> const& points = green.outer();  
 for (j=0, std::deque<point_t>::size_type i = 0; i < points.size(); ++i)  
  {  
  a = points[i];  
  std::cout << j++ << " [" << a[0] << " " << a[1] << "] " << std::endl;  
  }  
  
return 0;  
}  
```  
  
How count number of points? number of segment and how acessing variable 'a' ?  
In my opinion my shape have 8 point no 6

---

## Comment 1

> Username: mloskot  
> Created at: 2020-06-18 14:05:13 UTC  
> Url: https://github.com/boostorg/geometry/issues/729#issuecomment-646041179  

Your polygon has two rings in total, one exterior and one interior.  
Your polygon has 8 points in total.  
Your polygon is NOT valid, because the rings are not closed.  
  
You seem to be expecting that Boost.Geometry reports you number of **unique** points.  
From Boost.Geometry perspective, this is useless information, because each ring must be closed: first and last point must be repeated.

---

## Comment 2

> Username: 0xtrzy  
> Created at: 2020-06-18 15:37:44 UTC  
> Updated at: 2020-06-18 15:43:09 UTC  
> Url: https://github.com/boostorg/geometry/issues/729#issuecomment-646105801  

> Your polygon has two rings in total, one exterior and one interior.  
  
two ring, separately no interior ABCD are not touch EFGH  
  
![Zrzut ekranu z 2020-06-18 17-42-07](https://user-images.githubusercontent.com/59196690/85042123-1dfe5880-b18b-11ea-9fa4-806ed30e506f.png)  
  
> Your polygon has 8 points in total.  
  
and 8 segment  
boost return 6 and 8  
  
> Your polygon is NOT valid, because the rings are not closed.  
  
All ring are closed  
   
> You seem to be expecting that Boost.Geometry reports you number of **unique** points.  
  
all point are unique

---

## Comment 3

> Username: mloskot  
> Created at: 2020-06-18 15:43:27 UTC  
> Url: https://github.com/boostorg/geometry/issues/729#issuecomment-646110259  

> two ring, separately no interior  
  
Then your polygon is not a valid polygon, but a multipolygon.  
  
> All ring are closed  
  
No, they are not.  
Given your geometry is specified as `POLYGON((0 4, 3 1, 6 4, 3 0)(0 3, 0.1 3, 0.1 3.5, 0 3.5))`, then   
- The `(0 4, 3 1, 6 4, 3 0)` is not closed.  
- It would be closed if it was specied as `(0 4, 3 1, 6 4, 3 0, 0 4)`.  
- The similar issue applies to the second ring.

---

## Comment 4

> Username: 0xtrzy  
> Created at: 2020-06-18 16:01:01 UTC  
> Url: https://github.com/boostorg/geometry/issues/729#issuecomment-646123567  

ok i delete second polygon, how get acess to 'a'  
  
```  
#include <iostream>  
#include <deque>  
  
#include <boost/geometry.hpp>  
#include <boost/geometry/geometries/point_xy.hpp>  
#include <boost/geometry/geometries/polygon.hpp>  
  
#include <boost/foreach.hpp>  
  
typedef boost::geometry::model::d2::point_xy<double> point_t;  
typedef boost::geometry::model::polygon<point_t> polygon_t;  
  
int main()  
{  
polygon_t green;  
point_t   a;  
int       j = 0;  
  
 //BOOST_GEOMETRY_REGISTER_MULTI_POLYGON(std::deque<polygon_t>);  
  
// boost::geometry::read_wkt("POLYGON((0 4, 3 1, 6 4, 3 0, 0 4)(0 3, 0.1 3, 0.1 3.5, 0 3.5, 0 3))", green);  
 boost::geometry::read_wkt("POLYGON((0 4, 3 1, 6 4, 3 0, 0 4))", green);  
 boost::geometry::correct(green);  
  
 std::cout << "green: segment=" << boost::geometry::num_segments(green) << " point=" << boost::geometry::num_points(green) << std::endl;  
 std::string msg;  
 bool valid = boost::geometry::is_valid(green, msg);  
 std::cout << "is valid? " << (valid ? "yes" : "no") << std::endl;  
 if(!valid){ std::cout << "why not valid? " << msg << std::endl; }  
  
 std::deque<point_t> const& points = green.outer();  
 for (j=0, std::deque<point_t>::size_type i = 0; i < points.size(); ++i)  
  {  
  a = points[i];  
  std::cout << j++ << " [" << a[0] << " " << a[1] << "] " << std::endl;  
  }  
  
return 0;  
}  
  
```

---

## Comment 5

> Username: mloskot  
> Created at: 2020-06-18 17:36:12 UTC  
> Url: https://github.com/boostorg/geometry/issues/729#issuecomment-646207412  

The library offers multiple methods to access individual points and their coordinates, for example:  
  
```cpp  
auto outer_ring = green.outer();  
for (auto& p : outer_ring)  
    std::cout << p.x() << '\t' << p.y() << std::endl;  
```  
  
```cpp  
auto outer_ring = green.outer();  
for (auto i = std::begin(outer_ring); i != std::end(outer_ring); ++i)  
    std::cout << << i->x() << '\t' << i->y() << std::endl;  
```  
  
```cpp  
auto outer_ring = green.outer();  
for (auto& p : outer_ring)  
    std::cout << boost::geometry::get<0>(p) << '\t' << boost::geometry::get<1>(p) << std::endl;  
```  
  
Also, `green.outer();` can be replaced with `boost::geometry::exterior_ring(green);` as equivalent.

---

## Comment 6

> Username: 0xtrzy  
> Created at: 2020-06-18 17:41:42 UTC  
> Updated at: 2020-06-18 17:43:50 UTC  
> Url: https://github.com/boostorg/geometry/issues/729#issuecomment-646210674  

what get .x from 'a' from code?  
  
If I good undestand in polygon exist one and only one .outer()  
  
how access every .inner() ?

---

## Comment 7

> Username: mloskot  
> Created at: 2020-06-18 17:58:51 UTC  
> Updated at: 2020-06-18 17:59:21 UTC  
> Url: https://github.com/boostorg/geometry/issues/729#issuecomment-646219214  

> what get .x from 'a' from code?  
  
There are two equivalent ways to access X or Y of a point:  
  
```cpp  
point_t a;  
a.x(); // X coordinate of point  
boost::geometry::get<0>(); // X coordinate of point  
```  
  
> If I good undestand in polygon exist one and only one .outer()  
  
Correct  
  
> how access every .inner() ?  
  
See docs, with example https://www.boost.org/doc/libs/1_73_0/libs/geometry/doc/html/geometry/reference/models/model_polygon.html  
  
where you can see use of `poly1.inners()[0]` that suggests, `inners()` returns a vector of rings.  
  
```cpp  
for (auto& r : green.inners())  
{  
    for (auto& p : r)  
    {  
        p.x();  
        p.y();  
    }  
}  
  
```

---

## Comment 8

> Username: awulkiew  
> Created at: 2020-06-18 21:19:15 UTC  
> Url: https://github.com/boostorg/geometry/issues/729#issuecomment-646311138  

I think the confusing part is the definition of "Closed". In the closed polygon the last point in a ring is equal to the first one. If you want to be sure that the points are in the correct order and that the polygon is properly closed or opened just call `bg::correct()` after loading the points into it.
