# #503 - warning: unused parameter [Closed]

> Username: kivadiu  
> Created at: 2018-07-29 18:20:59 UTC  
> Updated at: 2018-07-30 19:48:51 UTC  
> Closed at: 2018-07-29 19:29:18 UTC  
> Url: https://github.com/boostorg/geometry/issues/503  

When compiled on linux x86_64 with clang++ 5.0.2 and this command line:  
`clang++ -o geometry.o -c -O2 -Wall -Wextra -std=c++14 -I/softs/lin64-clang-5.0.2/release/boost/include geometry.cpp`  
  
This simple program  
```  
#include <boost/geometry/strategies/spherical/intersection.hpp>  
int main() {  
  return 0;  
}  
```  
produces these warnings:  
```  
In file included from geometry.cpp:1:  
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
/softs/lin64-clang-5.0.2/release/boost/include/boost/geometry/strategies/spherical/intersection.hpp:544:81: warning: unused parameter 'b2' [-Wunused-parameter]  
                                                Point2 const& b1, Point2 const& b2, // in  
                                                                                ^  
7 warnings generated.  
```

---

## Comment 1

> Username: awulkiew  
> Created at: 2018-07-29 19:28:58 UTC  
> Url: https://github.com/boostorg/geometry/issues/503#issuecomment-408699988  

Since https://github.com/boostorg/geometry/issues/504 contains these warnings I'll consider this PR as a duplicate.
