# #1181 - Union result of polygons seems not right [Closed]

> Username: 0x8A63F77D  
> Created at: 2023-08-10 10:55:36 UTC  
> Updated at: 2023-08-15 02:26:31 UTC  
> Closed at: 2023-08-15 02:26:31 UTC  
> Url: https://github.com/boostorg/geometry/issues/1181  

I pasted the code to [https://godbolt.org/z/TM4h1MaeW](https://godbolt.org/z/TM4h1MaeW).  
  
```c++  
#include <iostream>  
#include <vector>  
#include <boost/geometry.hpp>  
#include <boost/geometry/geometries/polygon.hpp>  
namespace bg = boost::geometry;  
using point = bg::model::d2::point_xy<double>;  
using polygon = bg::model::polygon<point>;  
using multiPoly = bg::model::multi_polygon<polygon>;  
int main()  
{  
    auto poly1 = bg::from_wkt<multiPoly>("MULTIPOLYGON(((0 0, 0 1, 3 1, 3 0)))");  
    auto poly2 = bg::from_wkt<multiPoly>("MULTIPOLYGON(((1 -1, 1 2, 2 2, 2 -1)))");  
    multiPoly output;  
    bg::union_(poly1, poly2, output);  
    std::cout << bg::area(output);  
    return 0;  
}  
```  
  
The area of output is 4, which should be 5.  
And the visualization result shows that the bottom part of poly2 is missing. I am new to boost::geometry. Did I do something wrong?  
![GeoWatch](https://github.com/boostorg/geometry/assets/11420474/dad67daa-24a9-4ffc-8601-cbc5840157d8)

---

## Comment 1

> Username: AndreMeyerRMC  
> Created at: 2023-08-14 11:31:17 UTC  
> Url: https://github.com/boostorg/geometry/issues/1181#issuecomment-1677152594  

It works if you close the polygons. Try  
  
```  
    auto poly1 = bg::from_wkt<multiPoly>("MULTIPOLYGON(((0 0, 0 1, 3 1, 3 0, 0 0)))");  
    auto poly2 = bg::from_wkt<multiPoly>("MULTIPOLYGON(((1 -1, 1 2, 2 2, 2 -1, 1 -1)))");  
```

---

## Comment 2

> Username: 0x8A63F77D  
> Created at: 2023-08-14 13:35:47 UTC  
> Url: https://github.com/boostorg/geometry/issues/1181#issuecomment-1677331838  

> ```c++  
> bg::model::polygon  
> ```  
It works. Thank you.  
But I think this should be a bug since the default template argument `Closed` of bg::model::polygon is true, so it's implicit closed.

---

## Comment 3

> Username: barendgehrels  
> Created at: 2023-08-14 16:07:31 UTC  
> Url: https://github.com/boostorg/geometry/issues/1181#issuecomment-1677625538  

If `Closed` is true, then it expects all polygons to be closed.  
They are not closed automatically.
