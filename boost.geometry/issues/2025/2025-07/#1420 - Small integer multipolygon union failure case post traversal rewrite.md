# #1420 - Small integer multipolygon union failure case post traversal rewrite [Open]

> Username: tinko92  
> Created at: 2025-07-30 06:11:29 UTC  
> Updated at: 2025-07-30 12:23:05 UTC  
> Url: https://github.com/boostorg/geometry/issues/1420  

Here is a very small failure case for union that first fails on commit 805ff654e869030c4ba70f37fad6c3825f59c077, i.e. the traversal rewrite.  
  
```cpp  
#include <boost/geometry.hpp>  
  
namespace bg = boost::geometry;  
using point = bg::model::d2::point_xy<int>;  
using polygon = bg::model::polygon<point>;  
using multi_polygon = bg::model::multi_polygon<polygon>;  
  
int main() {  
  const auto geo1 = bg::from_wkt<multi_polygon>("MULTIPOLYGON(((4 6,2 6,2 4,0 4,0 8,4 8,6 8,6 6,6 4,4 4,4 6)),((2 2,2 4,4 4,4 2,2 2)),((2 2,2 0,0 0,0 2,2 2)),((4 2,6 2,6 0,4 0,4 2)))");  
  const auto geo2 = bg::from_wkt<multi_polygon>("MULTIPOLYGON(((2 2,2 4,4 4,4 2,2 2)))");  
  multi_polygon res;  
  bg::union_(geo1, geo2, res);  
  std::cout << bg::to_wkt(res) << '\n';  
  return 0;  
}  
```  
Prints   
```sql  
MULTIPOLYGON(((2 4,0 4,0 8,4 8,6 8,6 6,6 4,4 4,4 2,2 2,2 4)),((2 2,2 0,0 0,0 2,2 2)),((4 2,6 2,6 0,4 0,4 2)))  
```  
should print  
```sql  
MULTIPOLYGON(((2 4,0 4,0 8,4 8,6 8,6 6,6 4,4 4,4 6,2 6,2 4)),((2 4,4 4,4 2,2 2,2 4)),((2 2,2 0,0 0,0 2,2 2)),((4 2,6 2,6 0,4 0,4 2)))  
```  
  
Should be unrelated to numerical issues due to use of small integer coordinates.
