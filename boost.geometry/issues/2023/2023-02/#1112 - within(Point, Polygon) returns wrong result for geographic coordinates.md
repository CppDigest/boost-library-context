# #1112 - within(Point, Polygon) returns wrong result for geographic coordinates [Open]

> Username: tobiass-sdl  
> Created at: 2023-02-03 15:48:55 UTC  
> Updated at: 2023-07-23 00:12:36 UTC  
> Url: https://github.com/boostorg/geometry/issues/1112  

```  
typedef boost::geometry::model::d2::point_xy<double, boost::geometry::cs::geographic<boost::geometry::degree>> Point;  
typedef boost::geometry::model::polygon<Point> Polygon;  
  
  Polygon p;  
  Point p1(21.0005, 3.00053);  
  Point p2(21.0005, 1.99947);  
  Point p3(19.9995, 1.99947);  
  Point p4(19.9995, 3.00053);  
  Point p5(21.0005, 3.00053);  
  
  boost::geometry::append(p.outer(), p1);  
  boost::geometry::append(p.outer(), p2);  
  boost::geometry::append(p.outer(), p3);  
  boost::geometry::append(p.outer(), p4);  
  boost::geometry::append(p.outer(), p5);  
  
  Point testPoint(20, -177.5);  
  
  assert(boost::geometry::is_valid(p));  
  assert(!boost::geometry::within(testPoint, p));  
```  
  
the second assert fails. As the testPoint is on the other side of the planet this is clearly wrong.  
  
branch: develop.

---

## Comment 1

> Username: tobiass-sdl  
> Created at: 2023-02-03 15:56:59 UTC  
> Url: https://github.com/boostorg/geometry/issues/1112#issuecomment-1416063405  

It also seems that within returns always true for geographic coordinates if y == 90 or y == -90:  
```  
typedef boost::geometry::model::d2::point_xy<double, boost::geometry::cs::geographic<boost::geometry::degree>> Point;  
typedef boost::geometry::model::polygon<Point> Polygon;  
  
Polygon p;  
Point p1(21.0005, 93.00053);  
Point p2(21.0005, 81.99947);  
Point p3(19.9995, 81.99947);  
Point p4(19.9995, 93.00053);  
Point p5(21.0005, 93.00053);  
  
boost::geometry::append(p.outer(), p1);  
boost::geometry::append(p.outer(), p2);  
boost::geometry::append(p.outer(), p3);  
boost::geometry::append(p.outer(), p4);  
boost::geometry::append(p.outer(), p5);  
  
Point testPoint(20, 90.0);  
  
assert(boost::geometry::is_valid(p));  
assert(boost::geometry::within(testPoint, p));  
```  
  
testPoint.y() == 89.99 works fine.

---

## Comment 2

> Username: tobiass-sdl  
> Created at: 2023-02-04 10:09:58 UTC  
> Url: https://github.com/boostorg/geometry/issues/1112#issuecomment-1416714331  

I just realized that boost geometry uses lon, lat instead the lat, lon known from GPS and other systems.  
Swapping x and y fixed the issue.  
It might be a good idea to extend the documentation to clearify that: https://www.boost.org/doc/libs/1_81_0/libs/geometry/doc/html/geometry/reference/cs/cs_geographic.html

---

## Comment 3

> Username: awulkiew  
> Created at: 2023-07-23 00:11:26 UTC  
> Url: https://github.com/boostorg/geometry/issues/1112#issuecomment-1646697897  

Thanks for the report and the update.  
  
It's not really a feature of the coordinate system. Rather it's caused by the fact that `d2::point_xy` takes x before y in the constructor and that the library assumes that the coordinate of id 0 is x or longitude while the coordinate of id 1 is y or latitude. Note that you could write your own point type taking the coordinates in whatever order. But still longitude would have to be returned from `get<0>(pt)` and latitude from `get<1>(pt)`. A better place to describe it would probably be:  
https://www.boost.org/libs/geometry/doc/html/geometry/reference/access/get.html  
and/or  
https://www.boost.org/libs/geometry/doc/html/geometry/reference/concepts/concept_point.html
