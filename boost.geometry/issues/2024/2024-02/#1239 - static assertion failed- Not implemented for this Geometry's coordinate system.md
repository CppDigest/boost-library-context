# #1239 - static assertion failed: Not implemented for this Geometry's coordinate system [Closed]

> Username: plopresti  
> Created at: 2024-02-05 17:48:26 UTC  
> Updated at: 2024-02-06 13:13:36 UTC  
> Closed at: 2024-02-06 13:13:36 UTC  
> Url: https://github.com/boostorg/geometry/issues/1239  

The code below compiles fine on Boost versions prior to 1.76.0. On 1.76.0 and later, the compilation fails with:  
  
```  
/opt/compiler-explorer/libs/boost_1_84_0/boost/geometry/strategies/relate/services.hpp:36:5: error: static assertion failed: Not implemented for this Geometry's coordinate system.  
   36 |     BOOST_GEOMETRY_STATIC_ASSERT_FALSE(  
  
```  
  
I am a little surprised that anything is not implemented for the Cartesian coordinate system, but perhaps I am doing something wrong (?)  
  
Live link to Godbolt:  
  
https://godbolt.org/z/GTdzbbhnE  
  
Offending code:  
  
```  
#include <boost/geometry/geometries/point.hpp>  
#include <boost/geometry/geometries/box.hpp>  
#include <boost/geometry/index/rtree.hpp>  
  
#include <utility>  
#include <vector>  
#include <iterator>  
  
class MyIndex  
{  
 public:  
  
    typedef boost::geometry::cs::cartesian cartesian;  
    typedef boost::geometry::model::point<double, 2, cartesian> Point;  
    typedef boost::geometry::model::box<Point> Box;  
    typedef std::pair<Box, const void *> value_type;  
    typedef boost::geometry::index::rstar<16> rstar;  
    typedef boost::geometry::index::rtree<value_type, rstar> RTree;  
  
    void query(double x_low, double y_low, double x_high, double y_high,  
               std::vector<value_type> &vec) const;  
  
    RTree rtree_;  
};  
  
void  
MyIndex::query(const double x_low, const double y_low,  
               const double x_high, const double y_high,  
               std::vector<MyIndex::value_type> &vec) const  
{  
  const Box box(Point(x_low, y_low), Point(x_high, y_high));  
  rtree_.query(boost::geometry::index::intersects(box),  
               std::back_inserter(vec));  
}  
  
std::vector<MyIndex::value_type> doit(const MyIndex &jobdeck_index)  
{  
  std::vector<MyIndex::value_type> vec;  
  jobdeck_index.query(0, 0, 1, 1, vec);  
  return vec;  
}  
```

---

## Comment 1

> Username: plopresti  
> Created at: 2024-02-05 18:43:16 UTC  
> Url: https://github.com/boostorg/geometry/issues/1239#issuecomment-1927787559  

Addendum:  
  
`#include <boost/geometry.hpp>`  
  
...resolves the issue. The documentation does not seem to say that this is necessary, but it is a simple enough work-around.
