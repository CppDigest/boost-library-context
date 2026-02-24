# #896 - Assertion failure in get_turns with BOOST_GEOMETRY_NO_ROBUSTNESS. [Closed]

> Username: awulkiew  
> Created at: 2021-08-07 13:30:07 UTC  
> Updated at: 2021-10-26 15:33:02 UTC  
> Closed at: 2021-10-26 15:33:02 UTC  
> Url: https://github.com/boostorg/geometry/issues/896  

This assertion fails:  
https://github.com/boostorg/geometry/blob/develop/include/boost/geometry/algorithms/detail/overlay/get_turns.hpp#L148  
called from here:  
https://github.com/boostorg/geometry/blob/develop/include/boost/geometry/algorithms/detail/overlay/get_turn_info.hpp#L244-L245  
for:  
```  
#define BOOST_GEOMETRY_NO_ROBUSTNESS  
#include <boost/geometry.hpp>  
  
namespace bg = boost::geometry;  
namespace bgm = boost::geometry::model;  
  
using cpt_t = bgm::point<double, 2, bg::cs::cartesian>;  
using cls_t = bgm::linestring<cpt_t>;  
using cmls_t = bgm::multi_linestring<cls_t>;  
  
cmls_t mls;  
bg::read_wkt("MULTILINESTRING((0 0,10 10),(0 0,10 10,20 20))", mls);  
bg::is_simple(mls);  
```
