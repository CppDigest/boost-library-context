# #982 - disjoint of ring/box can give errors [Closed]

> Username: barendgehrels  
> Created at: 2022-02-26 12:11:32 UTC  
> Updated at: 2022-04-13 10:23:57 UTC  
> Closed at: 2022-04-13 10:23:57 UTC  
> Url: https://github.com/boostorg/geometry/issues/982  

See next code:  
```  
void test_disjoint_box_ring()  
{  
  using Point = boost::geometry::model::point<double, 2, boost::geometry::cs::cartesian>;  
  using Ring = boost::geometry::model::ring<Point, true, false>;  
  using Box = boost::geometry::model::box<Point>;  
  
  Ring ring;  
  boost::geometry::append(ring, Point{139.655, 35.346});  
  boost::geometry::append(ring, Point{139.618, 35.342});  
  boost::geometry::append(ring, Point{139.623, 35.358});  
  boost::geometry::correct(ring);  
  
  Box box{{139.62, 35.34}, {139.63, 35.35}};  
  
  std::cout << "disjoint ring/box: " << std::boolalpha << boost::geometry::disjoint(ring, box) << std::endl;  
  
  Ring ring_of_box;  
  {  
    // Convert box-to-ring  
  
    // This code does compile but does not work:  
    //boost::geometry::convert(box, ring);  
  
    // So use WKT, which works  
    std::ostringstream out;  
    out << boost::geometry::wkt(box);  
    boost::geometry::read_wkt(out.str(), ring_of_box);  
    boost::geometry::correct(ring_of_box);  
    std::cout << boost::geometry::area(box) << " " << boost::geometry::area(ring_of_box) << std::endl;  
  }  
    
  std::cout << "ring-of-box: " << boost::geometry::wkt(ring_of_box) << std::endl;  
  
  std::cout << "disjoint ring/ring: " << boost::geometry::disjoint(ring, ring_of_box) << std::endl;  
  
  {  
    std::vector<Ring> intersections;  
    boost::geometry::intersection(ring, box, intersections);  
    std::cout << "intersections ring/box size: " << intersections.size() << std::endl;  
    for (const auto &p : intersections)  
    {  
      std::cout << "Intersection ring/box: " << boost::geometry::area(p) << std::endl;  
    }  
  }    
  {  
    std::vector<Ring> intersections;  
    boost::geometry::intersection(ring, ring_of_box, intersections);  
    std::cout << "intersections ring/ring size: " << intersections.size() << std::endl;  
    for (const auto &p : intersections)  
    {  
      std::cout << "Intersection ring/ring: " << boost::geometry::area(p) << std::endl;  
    }  
  }    
}  
```  
Delivering:  
```  
disjoint ring/box: true  
0.0001 0.0001  
ring-of-box: POLYGON((139.62 35.34,139.62 35.35,139.63 35.35,139.63 35.34))  
disjoint ring/ring: false  
intersections ring/box size: 1  
Intersection ring/box: 7.20324e-05  
intersections ring/ring size: 1  
Intersection ring/ring: 7.20324e-05  
```  
  
And you can see that the `disjoint` function does not work correctly for this case of `ring` / `box`  
(I did not test other cases)

---

## Comment 1

> Username: Mitsuhiko-Matsukawa  
> Created at: 2022-04-08 05:24:16 UTC  
> Updated at: 2022-04-08 05:27:55 UTC  
> Url: https://github.com/boostorg/geometry/issues/982#issuecomment-1092453638  

I have a same problem.  
This is my simple test code.  
```  
#include <boost/geometry.hpp>  
#include <boost/geometry/geometries/box.hpp>  
#include <boost/geometry/geometries/ring.hpp>  
  
int main()  
{  
    namespace bg = boost::geometry;  
    using Point = bg::model::d2::point_xy<double>;  
    using Box = bg::model::box<Point>;  
    using Ring = bg::model::ring<Point, false, false>;  
  
    Box box(Point(1, 1), Point(4, 4));  
    Ring ring = {  
        Point(0, 2),  
        Point(2, 2),  
        Point(2, 3),  
        Point(0, 3),  
    };  
          
    assert(bg::intersects(box, ring));  // assertion fails  
  
    return 0;  
}  
```  
  
I've found the code of this bug.  
https://github.com/boostorg/geometry/blob/develop/include/boost/geometry/algorithms/for_each.hpp#L280  
The return code isn't used. It should be below.  
`if (!fe_segment_range_with_closure<closed>::apply(range, f)) return false;`
