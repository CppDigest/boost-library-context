# #504 - warning: unused parameter (more) [Closed]

> Username: kivadiu  
> Created at: 2018-07-29 18:36:24 UTC  
> Updated at: 2018-07-30 19:49:49 UTC  
> Closed at: 2018-07-30 19:49:49 UTC  
> Url: https://github.com/boostorg/geometry/issues/504  

When compiled on linux x86_64 with clang++ 5.0.2 and this command line:  
`clang++ -o geometry.o -c -O2 -DNDEBUG -Wall -Wextra -std=c++14 -I/softs/lin64-clang-5.0.2/release/boost/include geometry.cpp`  
This simple program  
```  
#include <boost/geometry/algorithms/within.hpp>  
int main() {  
  return 0;  
}  
```  
produces these warnings (2 more compared to issue #503):  
```  
In file included from geometry.cpp:1:  
In file included from /softs/lin64-clang-5.0.2/release/boost/include/boost/geometry/algorithms/within.hpp:24:  
In file included from /softs/lin64-clang-5.0.2/release/boost/include/boost/geometry/algorithms/detail/within/implementation.hpp:44:  
In file included from /softs/lin64-clang-5.0.2/release/boost/include/boost/geometry/algorithms/detail/within/multi_point.hpp:25:  
In file included from /softs/lin64-clang-5.0.2/release/boost/include/boost/geometry/algorithms/envelope.hpp:22:  
In file included from /softs/lin64-clang-5.0.2/release/boost/include/boost/geometry/algorithms/detail/envelope/interface.hpp:34:  
In file included from /softs/lin64-clang-5.0.2/release/boost/include/boost/geometry/strategies/cartesian/envelope_segment.hpp:15:  
/softs/lin64-clang-5.0.2/release/boost/include/boost/geometry/algorithms/detail/envelope/segment.hpp:156:61: warning: unused parameter 'lon1' [-Wunused-parameter]  
    static inline void compute_box_corners(CalculationType& lon1,  
                                                            ^  
/softs/lin64-clang-5.0.2/release/boost/include/boost/geometry/algorithms/detail/envelope/segment.hpp:158:61: warning: unused parameter 'lon2' [-Wunused-parameter]  
                                           CalculationType& lon2,  
                                                            ^  
In file included from geometry.cpp:1:  
In file included from /softs/lin64-clang-5.0.2/release/boost/include/boost/geometry/algorithms/within.hpp:24:  
In file included from /softs/lin64-clang-5.0.2/release/boost/include/boost/geometry/algorithms/detail/within/implementation.hpp:46:  
In file included from /softs/lin64-clang-5.0.2/release/boost/include/boost/geometry/algorithms/relate.hpp:15:  
In file included from /softs/lin64-clang-5.0.2/release/boost/include/boost/geometry/algorithms/detail/relate/implementation.hpp:24:  
In file included from /softs/lin64-clang-5.0.2/release/boost/include/boost/geometry/algorithms/detail/relate/linear_linear.hpp:32:  
In file included from /softs/lin64-clang-5.0.2/release/boost/include/boost/geometry/algorithms/detail/relate/turns.hpp:20:  
In file included from /softs/lin64-clang-5.0.2/release/boost/include/boost/geometry/algorithms/detail/overlay/get_turns.hpp:48:  
In file included from /softs/lin64-clang-5.0.2/release/boost/include/boost/geometry/strategies/intersection_strategies.hpp:30:  
In file included from /softs/lin64-clang-5.0.2/release/boost/include/boost/geometry/strategies/spherical/intersection.hpp:46:  
In file included from /softs/lin64-clang-5.0.2/release/boost/include/boost/geometry/strategies/spherical/point_in_poly_winding.hpp:32:  
In file included from /softs/lin64-clang-5.0.2/release/boost/include/boost/geometry/strategies/spherical/ssf.hpp:26:  
In file included from /softs/lin64-clang-5.0.2/release/boost/include/boost/geometry/strategies/spherical/disjoint_segment_box.hpp:30:  
In file included from /softs/lin64-clang-5.0.2/release/boost/include/boost/geometry/algorithms/detail/disjoint/segment_box.hpp:38:  
/softs/lin64-clang-5.0.2/release/boost/include/boost/geometry/formulas/vertex_longitude.hpp:340:32: warning: unused parameter 'lon1' [-Wunused-parameter]  
    static inline CT apply(CT& lon1,  
                               ^  
/softs/lin64-clang-5.0.2/release/boost/include/boost/geometry/formulas/vertex_longitude.hpp:341:32: warning: unused parameter 'lat1' [-Wunused-parameter]  
                           CT& lat1,  
                               ^  
/softs/lin64-clang-5.0.2/release/boost/include/boost/geometry/formulas/vertex_longitude.hpp:343:32: warning: unused parameter 'lat2' [-Wunused-parameter]  
                           CT& lat2,  
                               ^  
/softs/lin64-clang-5.0.2/release/boost/include/boost/geometry/formulas/vertex_longitude.hpp:344:38: warning: unused parameter 'vertex_lat' [-Wunused-parameter]  
                           CT const& vertex_lat,  
                                     ^  
/softs/lin64-clang-5.0.2/release/boost/include/boost/geometry/formulas/vertex_longitude.hpp:345:32: warning: unused parameter 'alp1' [-Wunused-parameter]  
                           CT& alp1,  
                               ^  
/softs/lin64-clang-5.0.2/release/boost/include/boost/geometry/formulas/vertex_longitude.hpp:346:44: warning: unused parameter 'azimuth_strategy' [-Wunused-parameter]  
                           Strategy const& azimuth_strategy)  
                                           ^  
In file included from geometry.cpp:1:  
In file included from /softs/lin64-clang-5.0.2/release/boost/include/boost/geometry/algorithms/within.hpp:24:  
In file included from /softs/lin64-clang-5.0.2/release/boost/include/boost/geometry/algorithms/detail/within/implementation.hpp:46:  
In file included from /softs/lin64-clang-5.0.2/release/boost/include/boost/geometry/algorithms/relate.hpp:15:  
In file included from /softs/lin64-clang-5.0.2/release/boost/include/boost/geometry/algorithms/detail/relate/implementation.hpp:24:  
In file included from /softs/lin64-clang-5.0.2/release/boost/include/boost/geometry/algorithms/detail/relate/linear_linear.hpp:32:  
In file included from /softs/lin64-clang-5.0.2/release/boost/include/boost/geometry/algorithms/detail/relate/turns.hpp:20:  
In file included from /softs/lin64-clang-5.0.2/release/boost/include/boost/geometry/algorithms/detail/overlay/get_turns.hpp:48:  
In file included from /softs/lin64-clang-5.0.2/release/boost/include/boost/geometry/strategies/intersection_strategies.hpp:30:  
/softs/lin64-clang-5.0.2/release/boost/include/boost/geometry/strategies/spherical/intersection.hpp:544:81: warning: unused parameter 'b2' [-Wunused-parameter]  
                                                Point2 const& b1, Point2 const& b2, // in  
                                                                                ^  
9 warnings generated.  
```

---

## Comment 1

> Username: awulkiew  
> Created at: 2018-07-30 19:49:49 UTC  
> Url: https://github.com/boostorg/geometry/issues/504#issuecomment-408987800  

Thanks!
