# #1222 - sym_difference with rectilinear integer coordinate polygons produces wrong result. [Closed]

> Username: tinko92  
> Created at: 2023-12-08 23:28:50 UTC  
> Updated at: 2024-11-21 07:18:53 UTC  
> Closed at: 2024-11-21 01:02:47 UTC  
> Url: https://github.com/boostorg/geometry/issues/1222  

For the sym_difference of   
MULTIPOLYGON(((2 4,2 2,0 2,0 4,2 4)),((6 4,4 4,2 4,2 6,0 6,0 10,6 10,6 4)))  
and  
MULTIPOLYGON(((4 4,4 2,2 2,2 4,4 4)),((4 8,4 6,2 6,2 8,4 8))),   
I get:  
MULTIPOLYGON(((4 4,4 2,2 2,0 2,0 4,4 4)))  
  
I expect:   
MULTIPOLYGON(((6 4,4 4,4 2,2 2,0 2,0 4,2 4,2 6,0 6,0 10,6 10,6 4),(2 6,4 6,4 8,2 8,2 6)))  
  
It occurs with integer coordinates and rectilinear shapes, so it does not look like it should be a numerical issue.  
  
Here is minimal code to reproduce the issue:  
```cpp  
#include <iostream>  
  
#include <boost/geometry.hpp>  
#include <boost/geometry/geometries/geometries.hpp>  
  
namespace bg = boost::geometry;  
  
using point = bg::model::point<int, 2, bg::cs::cartesian>;  
using polygon = bg::model::polygon<point>;  
using multipoly = bg::model::multi_polygon<polygon>;  
  
  
int main() {  
    auto p1 = bg::from_wkt<multipoly>("MULTIPOLYGON(((2 4,2 2,0 2,0 4,2 4)),((6 4,4 4,2 4,2 6,0 6,0 10,6 10,6 4)))");  
    auto p2 = bg::from_wkt<multipoly>("MULTIPOLYGON(((4 4,4 2,2 2,2 4,4 4)),((4 8,4 6,2 6,2 8,4 8)))");  
    multipoly p3;  
    bg::sym_difference(p1, p2, p3);  
    std::cout << bg::wkt(p3) << "\n";  
    return 0;  
}  
```  
  
In case, it is of any use, here is the code that was used to find a minimal example: https://gist.github.com/tinko92/ca944c0665dcdc15a6dc7bac147b088f  
  
Here is a picture of the two input sets, drawn with 30% opacity in red and blue.  
![screenshot2](https://github.com/boostorg/geometry/assets/17624459/5303355f-cd5c-424f-a2bb-5b21ba687faa)

---

## Comment 1

> Username: tinko92  
> Created at: 2024-11-21 01:02:47 UTC  
> Updated at: 2024-11-21 04:40:41 UTC  
> Url: https://github.com/boostorg/geometry/issues/1222#issuecomment-2489852735  

This does no longer reproduce with current develop (current is d7698b1c8a7250962f086e1749719e85d4dbecca but I didn't bisect to find the exact fix).

---

## Comment 2

> Username: barendgehrels  
> Created at: 2024-11-21 07:18:38 UTC  
> Updated at: 2024-11-21 07:18:53 UTC  
> Url: https://github.com/boostorg/geometry/issues/1222#issuecomment-2490248560  

@tinko92 thanks for testing!  
We probably should still include it in a unit test, I’ll put it in my next PR (later this week)
