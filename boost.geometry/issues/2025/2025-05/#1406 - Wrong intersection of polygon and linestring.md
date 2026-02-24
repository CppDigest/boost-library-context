# #1406 - Wrong intersection of polygon and linestring [Open]

> Username: vissarion  
> Created at: 2025-05-09 15:46:00 UTC  
> Updated at: 2025-05-12 13:15:46 UTC  
> Url: https://github.com/boostorg/geometry/issues/1406  

The following code  
  
```cpp  
#include <iostream>  
#include <vector>  
#include <boost/geometry.hpp>  
  
namespace bg = boost::geometry;  
  
int main() {  
    // Define the polygon  
    bg::model::polygon<bg::model::d2::point_xy<double>> polygon;  
    bg::read_wkt("POLYGON((1 1.5,3.5 4.5,4 6,2.5 4.5,1 1.5))", polygon);  
  
    // Define the linestring  
    bg::model::linestring<bg::model::d2::point_xy<double>> linestring;  
    bg::read_wkt("LINESTRING(1.5 1.5,0.5 1.5)", linestring);  
  
    // Compute the intersection  
    std::tuple<  
        bg::model::multi_point<bg::model::d2::point_xy<double>>,  
        bg::model::multi_linestring<bg::model::linestring<bg::model::d2::point_xy<double>>>  
    > output;  
    bg::intersection(polygon, linestring, output);  
  
    // Print the results  
    auto linestrings = std::get<1>(output);  
    auto points = std::get<0>(output);  
    std::cout << "Intersection linestrings: " << bg::wkt(linestrings) << std::endl;  
    std::cout << "Intersection points: " << bg::wkt(points) << std::endl;  
  
    return 0;  
}  
```  
  
Gives the following incorrect output:  
```  
Intersection linestrings: MULTILINESTRING((1 1.5,0.5 1.5))  
Intersection points: MULTIPOINT()  
```  
  
on current develop with clang-15 on ubuntu 22.  
  
![Image](https://github.com/user-attachments/assets/13c74026-7013-4a8e-8fbf-91e10dab3419)

---

## Comment 1

> Username: vissarion  
> Created at: 2025-05-12 13:10:03 UTC  
> Url: https://github.com/boostorg/geometry/issues/1406#issuecomment-2872517003  

Issue update: the polygon is invalid (incorrect orientation).   
If the corrected polygon is used i.e. `POLYGON((1 1.5,3.5 4.5,4 6,2.5 4.5,1 1.5))` then the result is empty (still wrong thought).  
  
```  
Intersection linestrings: MULTILINESTRING()  
Intersection points: MULTIPOINT()  
```

---

## Comment 2

> Username: vissarion  
> Created at: 2025-05-12 13:15:44 UTC  
> Url: https://github.com/boostorg/geometry/issues/1406#issuecomment-2872538376  

Similar issue, intersection of `POLYGON((1 1,3 5,4 6,3 4,1 1))` with `LINESTRING(0 0,1 1)`. Now the geometries intersect in a common vertex.
