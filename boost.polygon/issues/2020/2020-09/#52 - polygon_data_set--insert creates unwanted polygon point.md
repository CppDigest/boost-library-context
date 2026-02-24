# #52 - polygon_data_set::insert creates unwanted polygon point [Open]

> Username: todeschini-felipe  
> Created at: 2020-09-11 17:51:26 UTC  
> Updated at: 2021-01-29 01:58:12 UTC  
> Url: https://github.com/boostorg/polygon/issues/52  

I want to represent a donut shape with `boost::polygon`, such as the figure below, with the following points:  
![image](https://user-images.githubusercontent.com/10549195/92962734-c862e080-f447-11ea-9cde-5454b01fcbfa.png)  
```  
10 polygon : (0, 0), (10, 0), (10, 10), (8, 10), (8, 2), (2, 2), (2, 8), (8, 8), (8, 10), (0, 10),  
```  
  
Once I include the polygon into a [polygon set data](https://www.boost.org/doc/libs/1_73_0/libs/polygon/doc/gtl_custom_polygon_set.htm), however, it creates an additional unwanted point `(8,8)`  from  `(8,10)->(8,2)`. Even `boost::polygon::simplify` leaves this point.  Is that the expected behavior?  
  
```  
12 insert : (10, 10), (8, 10), (8, 8), (8, 2), (2, 2), (2, 8), (8, 8), (8, 10), (0, 10), (0, 0), (10, 0), (10, 10),  
12 simplf : (10, 10), (8, 10), (8, 8), (8, 2), (2, 2), (2, 8), (8, 8), (8, 10), (0, 10), (0, 0), (10, 0), (10, 10),   
```  
  
```  
#include <iostream>  
  
#include <boost/geometry.hpp>  
#include <boost/geometry/geometries/linestring.hpp>  
#include <boost/geometry/geometries/point_xy.hpp>  
#include <boost/polygon/polygon.hpp>  
  
#include <boost/assign.hpp>  
  
using namespace boost::assign;  
namespace gtl = boost::polygon;  
using namespace boost::polygon::operators;  
  
//--------------------------------------------------------------------------------  
// CPoint  
//--------------------------------------------------------------------------------  
struct CPoint {  
  //CPoint() : x(0), y(0) {}  
  //CPoint(int a, int b) : x(a), y(b) {}  
  
  int x;  
  int y;  
};  
  
namespace boost::polygon {  
template <>  
struct geometry_concept<CPoint> { typedef point_concept type; };  
template <>  
struct point_traits<CPoint> {  
  typedef int coordinate_type;  
  
  static inline coordinate_type get(const CPoint& point, orientation_2d orient) {  
    return ((orient == HORIZONTAL) ? point.x : point.y);  
  }  
};  
  
template <>  
struct point_mutable_traits<CPoint> {  
  typedef int coordinate_type;  
  
  static inline void set(CPoint& point, orientation_2d orient, int value) {  
    if(orient == HORIZONTAL)  
      point.x = value;  
    else  
      point.y = value;  
  }  
  static inline CPoint construct(int x_value, int y_value) {  
    CPoint retval;  
    retval.x = x_value;  
    retval.y = y_value;  
    return retval;  
  }  
};  
}  
  
//--------------------------------------------------------------------------------  
// CPolygon  
//--------------------------------------------------------------------------------  
typedef std::list<CPoint> CPolygon;  
  
namespace boost::polygon {  
template <>  
struct geometry_concept<CPolygon>{ typedef polygon_concept type; };  
  
template <>  
struct polygon_traits<CPolygon> {  
  typedef int coordinate_type;  
  typedef CPolygon::const_iterator iterator_type;  
  typedef CPoint point_type;  
  
  static inline iterator_type begin_points(const CPolygon& t) { return t.begin(); }  
  static inline iterator_type end_points(const CPolygon& t) { return t.end(); }  
  static inline std::size_t size(const CPolygon& t) { return t.size(); }  
  static inline winding_direction winding(const CPolygon& t) { return unknown_winding; }  
};  
  
template <>  
struct polygon_mutable_traits<CPolygon> {  
  template <typename iT>  
  static inline CPolygon& set_points(CPolygon& t, iT input_begin, iT input_end) {  
    t.clear();  
    while(input_begin != input_end) {  
      t.push_back(CPoint());  
      gtl::assign(t.back(), *input_begin);  
      ++input_begin;  
    }  
    return t;  
  }  
};  
}  
  
std::ostream &operator<<(std::ostream &os, const CPoint &p) {  
  return os << "(" << p.x << ", " << p.y << ")";  
}  
  
std::ostream &operator<<(std::ostream &os, const CPolygon &p) {  
  for (const auto point : p)  
    os << point << ", ";  
  return os;  
}  
  
  
int main()  
{  
  CPolygon donut{  
    CPoint{0, 0}, CPoint{10,0}, CPoint{10, 10},  
    CPoint{8, 10}, CPoint{8, 2},  
    CPoint{2, 2}, CPoint{2, 8}, CPoint{8, 8},  
   CPoint{8,10}, CPoint{0,10} };   
  
  std::cout << donut.size() << "  donut : " << donut << std::endl;  
  
  boost::polygon::polygon_set_data<int> ps;  
  std::vector<CPolygon> polygons;  
  ps.insert(donut);  
  ps.get(polygons);  
  for (const auto &pl : polygons)  
    std::cout << pl.size() << " insert : " << pl << std::endl;  
  
  polygons.clear();  
  boost::polygon::simplify(ps, 1e-3);  
  ps.get(polygons);  
  for (const auto &pl : polygons)  
    std::cout << pl.size() << " simplf : " << pl << std::endl;  
  
	return 0;  
}  
  
```

---

## Comment 1

> Username: by408  
> Created at: 2021-01-29 01:57:44 UTC  
> Updated at: 2021-01-29 01:58:12 UTC  
> Url: https://github.com/boostorg/polygon/issues/52#issuecomment-769520960  

ps.get(polygons); function applies a union operation to the polygons. This is by definition. If you add overlapping polygons and call get function you will get merged results, it won't match your original input.
