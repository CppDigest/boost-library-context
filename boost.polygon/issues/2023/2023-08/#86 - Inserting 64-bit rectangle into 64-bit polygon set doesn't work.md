# #86 - Inserting 64-bit rectangle into 64-bit polygon set doesn't work [Open]

> Username: plopresti  
> Created at: 2023-08-17 18:38:04 UTC  
> Updated at: 2023-08-17 22:00:21 UTC  
> Url: https://github.com/boostorg/polygon/issues/86  

[Godbolt live link](https://godbolt.org/z/of3cKqxYb)  
  
The following program inserts a 64-bit rectangle into a 64-bit polygon set. Then it does the same thing again, but using a polygon instead of a rectangle to represent the shape. It prints out whether each polygon set is empty following the insertion of the figure.  
  
Expected output:  
  
    pset1 empty == 0  
    pset2 empty == 0  
  
Actual output:  
  
    pset1 empty == 1  
    pset2 empty == 0  
  
In other words, inserting the figure as a rectangle drops it.  
  
Note that the figure is not dropped (and you get the expected output) if you change the values of the coordinates to fit in 32 bits.  
  
```  
#include <boost/polygon/polygon.hpp>  
  
#include <iostream>  
#include <cstdint>  
#include <vector>  
  
namespace bp = boost::polygon;  
  
typedef int64_t Coord;  
typedef bp::point_data<Coord> Point;  
typedef bp::polygon_data<Coord> Poly;  
typedef bp::rectangle_data<Coord> Rect;  
typedef bp::polygon_set_data<Coord> PolySet;  
  
int  
main()  
{  
    const int64_t xl = 1214688665600;  
    const int64_t xh = 1226223002624;  
    const int64_t yl = 1524039680000;  
    const int64_t yh = 1527821107200;  
  
    Rect r(xl, yl, xh, yh);  
    PolySet pset1;  
    pset1.insert(r);  
    std::cout << "pset1 empty == " << bp::empty(pset1) << "\n";  
  
    std::vector<Point> points;  
    points.push_back(Point(xl,yl));  
    points.push_back(Point(xl,yh));  
    points.push_back(Point(xh,yh));  
    points.push_back(Point(xh,yl));  
    Poly poly;  
    poly.set(points.begin(), points.end());  
    PolySet pset2;  
    pset2.insert(poly);  
    std::cout << "pset2 empty == " << bp::empty(pset2) << "\n";  
}  
```

---

## Comment 1

> Username: plopresti  
> Created at: 2023-08-17 22:00:21 UTC  
> Url: https://github.com/boostorg/polygon/issues/86#issuecomment-1683034456  

I chased this down to winding() returning the wrong answer for polygon_90_set_data<int64_t> objects due to 64-bit overflow when calculating the area. This causes insert_vertex_sequence to treat the polygon as a hole, making it disappear.  
  
Test program to illustrate the bug in winding() ([Godbolt](https://godbolt.org/z/EccrEbch7)):  
  
```  
include <boost/polygon/polygon.hpp>  
  
#include <iostream>  
#include <cstdint>  
  
namespace bp = boost::polygon;  
  
typedef int64_t Coord;  
typedef bp::polygon_data<Coord> Poly;  
typedef bp::polygon_90_data<Coord> Poly90;  
typedef bp::rectangle_data<Coord> Rect;  
  
template<typename T>  
static void dump(const T &poly)  
{  
    typedef typename bp::polygon_traits<T>::iterator_type It;  
    for (It it = begin_points(poly) ; it != end_points(poly) ; ++it)  
        std::cout << "x " << x(*it) << " y " << y(*it) << "\n";  
}  
  
int  
main()  
{  
    const int64_t xl = 1214688665600;  
    const int64_t xh = 1226223002624;  
    const int64_t yl = 1524039680000;  
    const int64_t yh = 1527821107200;  
  
    const Rect r(xl, yl, xh, yh);  
  
    Poly poly;  
    assign(poly, r);  
    dump(poly);  
    std::cout << "poly area == " << area(poly) << "\n";  
    std::cout << "poly winding == " << winding(poly).to_int() << "\n\n";  
  
    Poly90 poly2;  
    assign(poly2, r);  
    dump(poly2);  
    std::cout << "poly2 area == " << area(poly2) << "\n";  
    std::cout << "poly2 winding == " << winding(poly2).to_int() << "\n";  
}  
  
```  
  
The following patch corrects the behavior of these two test programs, but probably has unintended consequences:  
  
```  
--- a/include/boost/polygon/isotropy.hpp  
+++ b/include/boost/polygon/isotropy.hpp  
@@ -273,9 +273,6 @@ namespace boost { namespace polygon{  
   
   template <typename domain_type, typename coordinate_type>  
   struct area_type_by_domain { typedef typename coordinate_traits<coordinate_type>::area_type type; };  
-  template <typename coordinate_type>  
-  struct area_type_by_domain<manhattan_domain, coordinate_type> {  
-    typedef typename coordinate_traits<coordinate_type>::manhattan_area_type type; };  
   
   template<bool E, class R = void>  
   struct enable_if_ {  
```  
  
Possibly related: Issue #39
