# #1421 - Small integer multipolygon (sym_)difference failure case post traversal rewrite [Open]

> Username: tinko92  
> Created at: 2025-07-30 06:33:06 UTC  
> Updated at: 2025-07-30 06:33:06 UTC  
> Url: https://github.com/boostorg/geometry/issues/1421  

Here is a very small failure case for both difference and sym_difference that first fails on commit 805ff654e869030c4ba70f37fad6c3825f59c077, i.e. the traversal rewrite.  
  
```cpp  
#include <boost/geometry.hpp>  
  
namespace bg = boost::geometry;  
  
using point = bg::model::d2::point_xy<int>;  
using polygon = bg::model::polygon<point>;  
using multi_polygon = bg::model::multi_polygon<polygon>;  
  
int main() {  
  const auto geo1 = bg::from_wkt<multi_polygon>("MULTIPOLYGON(((8 8,8 6,8 4,6 4,6 2,6 0,0 0,0 6,2 6,2 8,8 8),(2 4,2 2,4 2,4 4,2 4),(6 6,4 6,4 4,6 4,6 6)))");  
  const auto geo2 = bg::from_wkt<multi_polygon>("MULTIPOLYGON(((6 6,6 4,4 4,4 6,6 6)),((6 6,6 8,8 8,8 6,6 6)),((4 6,2 6,2 8,4 8,4 6)),((4 4,4 2,2 2,2 4,4 4)),((4 2,6 2,6 0,4 0,4 2)),((2 6,2 4,0 4,0 6,2 6)),((2 2,2 0,0 0,0 2,2 2)),((6 2,6 4,8 4,8 2,6 2)))");  
  multi_polygon diff, symdiff;  
  bg::difference(geo1, geo2, diff);  
  bg::sym_difference(geo1, geo2, symdiff);  
  
  std::cout << "Diff:\t\t" << bg::to_wkt(diff) << '\n';  
  std::cout << "Symdiff:\t" << bg::to_wkt(symdiff) << '\n';  
  return 0;  
}  
```  
Prints   
```sql  
Diff:           MULTIPOLYGON(((8 6,8 4,6 4,6 6,8 6)),((2 2,4 2,4 0,2 0,2 2)),((2 4,2 2,0 2,0 4,2 4)),((6 6,4 6,4 8,6 8,6 6)))  
Symdiff:        MULTIPOLYGON(((8 2,6 2,6 4,4 4,4 6,4 8,6 8,6 6,8 6,8 2)),((4 0,2 0,2 2,0 2,0 4,4 4,4 0)))  
```  
should print  
```sql  
Diff:           MULTIPOLYGON(((8 6,8 4,6 4,6 6,8 6)),((4 0,2 0,2 2,4 2,4 0)),((0 4,2 4,2 2,0 2,0 4)),((6 8,6 6,4 6,4 8,6 8)),((6 4,6 2,4 2,4 4,6 4)),((2 6,4 6,4 4,2 4,2 6)))  
Symdiff:        MULTIPOLYGON(((8 2,4 2,4 0,2 0,2 2,0 2,0 4,2 4,2 6,4 6,4 8,6 8,6 6,8 6,8 2)))  
```
