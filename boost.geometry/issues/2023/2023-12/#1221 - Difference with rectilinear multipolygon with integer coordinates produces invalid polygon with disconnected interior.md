# #1221 - Difference with rectilinear multipolygon with integer coordinates produces invalid polygon with disconnected interior [Closed]

> Username: tinko92  
> Created at: 2023-12-08 22:50:06 UTC  
> Updated at: 2025-07-16 16:05:54 UTC  
> Closed at: 2025-07-16 16:05:54 UTC  
> Url: https://github.com/boostorg/geometry/issues/1221  

The code for reproduction is  
  
```cpp  
#include <iostream>  
#include <boost/geometry.hpp>  
#include <boost/geometry/geometries/geometries.hpp>  
  
namespace bg = boost::geometry;  
using point = bg::model::point<int, 2, bg::cs::cartesian>;  
using polygon = bg::model::polygon<point>;  
using multipoly = bg::model::multi_polygon<polygon>;  
  
int main() {  
    auto p1 = bg::from_wkt<multipoly>("MULTIPOLYGON(((1 2,1 3,0 3,0 4,1 4,1 5,3 5,3 0,0 0,0 2,1 2),(1 2,1 1,2 1,2 2,1 2)))");  
    auto p2 = bg::from_wkt<polygon>("POLYGON((1 3,1 4,2 4,2 3,1 3))");  
    multipoly p3;  
    bg::difference(p1, p2, p3);  
    std::cout << bg::is_valid(p1) << " , " << bg::is_valid(p2) << "\n";  
    std::cout << bg::is_valid(p3) << " , " << bg::wkt(p3) << "\n";  
    return 0;  
}  
```  
  
The output is   
```  
1 , 1  
0 , MULTIPOLYGON(((1 4,1 5,3 5,3 0,0 0,0 2,1 2,1 3,0 3,0 4,1 4),(1 4,1 3,2 3,2 4,1 4),(1 2,1 1,2 1,2 2,1 2)))  
```  
  
which covers the right area but is invalid, but it should be two polygons (looks like the first counter example in Figure 12 of the section on polygons in the OGC spec, page 28). If the hole from p1 is removed, a valid output is produced. If it is read as a polygon rather than as a multipolygon, the problem also disappears. It shouldn't be a numerical issue (I think) because the coordinates are integral.  
  
This is what the input looks like (p1 is semi-transparent blue, 2 is semi-transparent red):  
![screenshot2](https://github.com/boostorg/geometry/assets/17624459/c58a6edb-84f9-4a47-ba56-9bdd699bb2c9)  
  
And the result in green:  
![screenshot2](https://github.com/boostorg/geometry/assets/17624459/961dc87c-c1eb-4fb8-bfe1-bfd8c7bce0e5)

---

## Comment 1

> Username: tinko92  
> Created at: 2024-12-06 09:31:48 UTC  
> Url: https://github.com/boostorg/geometry/issues/1221#issuecomment-2522641885  

This issue is fixed on #1350 .

---

## Comment 2

> Username: barendgehrels  
> Created at: 2024-12-06 21:01:23 UTC  
> Url: https://github.com/boostorg/geometry/issues/1221#issuecomment-2524168370  

> This issue is fixed on [#1350](https://github.com/boostorg/geometry/pull/1350) .  
  
Confirmed. Failed before.

---

## Comment 3

> Username: vissarion  
> Created at: 2025-07-16 16:05:54 UTC  
> Url: https://github.com/boostorg/geometry/issues/1221#issuecomment-3079289474  

This is fixed by #1369
