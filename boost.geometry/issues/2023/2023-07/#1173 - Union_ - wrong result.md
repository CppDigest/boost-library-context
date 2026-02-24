# #1173 - Union_ : wrong result [Closed]

> Username: ghost  
> Created at: 2023-07-10 08:06:50 UTC  
> Updated at: 2023-07-24 06:34:13 UTC  
> Closed at: 2023-07-24 06:34:13 UTC  
> Url: https://github.com/boostorg/geometry/issues/1173  

Boost geo 1.81, linux  
  
Hi,  
Please try that code:  
  
```  
#include <iostream>  
#include <boost/geometry/algorithms/union.hpp>  
#include <boost/geometry/algorithms/correct.hpp>  
#include <boost/geometry/geometries/polygon.hpp>  
#include <boost/geometry/geometries/multi_polygon.hpp>  
  
using namespace std;  
  
using Vector2d = boost::geometry::model::d2::point_xy <float>;  
  
using pol2d = boost::geometry::model::polygon <Vector2d, false, false>;  
  
using mpol2d = boost::geometry::model::multi_polygon <pol2d>;  
  
auto main () -> int {  
  
        mpol2d mpol;  
        mpol2d out_tmp2;  
        pol2d left_side, top_side, bottom_side, right_side;  
  
        // -----------------  
        // Make the square  
        // ------------------  
  
        left_side.outer().push_back( {0.0, 0.0} );  
        left_side.outer().push_back( {1.0, 0.0} );  
        left_side.outer().push_back( {1.0, 10.0} );  
        left_side.outer().push_back( {0.0, 10.0} );  
  
        boost::geometry::correct( left_side );  
  
        top_side.outer().push_back( {0.0, 10.0} );  
        top_side.outer().push_back( {0.0, 9.0} );  
        top_side.outer().push_back( {10.0, 9.0} );  
        top_side.outer().push_back( {10.0, 10.0} );  
  
        boost::geometry::correct( top_side );  
  
        bottom_side.outer().push_back( {0.0, 0.0} );  
        bottom_side.outer().push_back( {10.0, 0.0} );  
        bottom_side.outer().push_back( {10.0, 1.0} );  
        bottom_side.outer().push_back( {0.0, 1.0} );  
  
        boost::geometry::correct( bottom_side );  
  
        right_side.outer().push_back( {9.0, 0.0} );  
        right_side.outer().push_back( {10.0, 0.0} );  
        right_side.outer().push_back( {10.0, 10.0} );  
        right_side.outer().push_back( {9.0, 10.0} );  
  
        boost::geometry::correct( right_side );  
  
        boost::geometry::union_( left_side, top_side, mpol );  
  
        boost::geometry::union_( mpol, bottom_side, out_tmp2 );  
  
        boost::geometry::union_( out_tmp2, right_side, mpol );  
  
        std::cout << "Num polygons in mpol: " << mpol.size() << "\n";  
  
}  
  
  
```  
  
The issue: mpol has 2 polygons when I should only have a single pol, with an outer + inner square.  
The second polygon contained in the mpol is correct though.  
  
Thanks

---

## Comment 1

> Username: awulkiew  
> Created at: 2023-07-22 23:49:15 UTC  
> Url: https://github.com/boostorg/geometry/issues/1173#issuecomment-1646693141  

Thanks for the report.  
It's intended behavior, i.e. the result is added to `mpol` which is not empty prior to the last `union_` call.  
@barendgehrels @vissarion correct me if I'm wrong.

---

## Comment 2

> Username: ghost  
> Created at: 2023-07-24 06:34:13 UTC  
> Url: https://github.com/boostorg/geometry/issues/1173#issuecomment-1647298072  

Yes, my bad,  
  
I forgot to call `mpol.clear();` before `boost::geometry::union_( out_tmp2, right_side, mpol );`.  
  
Closing  :+1:
