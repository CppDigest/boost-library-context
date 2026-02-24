# #864 - Geographic Intersection of Line with Box yields inconsistent behavior of bg::intersects and bg::intersection [Open]

> Username: ghost  
> Created at: 2021-06-08 10:12:16 UTC  
> Updated at: 2021-09-02 11:36:24 UTC  
> Url: https://github.com/boostorg/geometry/issues/864  

Dear maintainers of boost::geometry,  
  
thank you for taking care of #851 so quickly. 😄   
  
We have found another issue that is potentially related.  
We use the same line from Stuttgart to Berlin from the setup of #851.  
  
A box that intersects the geodesic is correctly reported as `bg::intersects`.  
However, the resulting `bg::intersect` set with the box that intersects the geodesic is empty.  
  
If we move the box towards the East so that it intersects the direct Cartesian line between the two coordinates, `bg::intersect` returns a non-empty set, however.  
  
This is the code (modified example from #851):  
  
``` C++  
#include <iostream>  
#include <boost/geometry.hpp>  
#include <boost/geometry/core/coordinate_system.hpp>  
#include <boost/geometry/geometries/point_xy.hpp>  
  
  
namespace bg = boost::geometry;  
namespace bm = bg::model::d2;  
namespace srs = bg::srs;  
  
using Pt = bm::point_xy<double, bg::cs::geographic<bg::degree> >;  
using Box = bg::model::box<Pt>;  
using LineString = bg::model::linestring<Pt>;  
  
using PtCart = bm::point_xy<double>;  
using BoxCart = bg::model::box<PtCart>;  
using LineStringCart = bg::model::linestring<PtCart>;  
  
Pt Stuttgart() { return { 9.18, 48.78}; }  
Pt Berlin() { return { 13.4, 52.52 }; }  
  
PtCart StuttgartCart() { return { 9.18, 48.78}; }  
PtCart BerlinCart() { return { 13.4, 52.52 }; }  
  
Box boxWest() {  
  return { {11.030273, 50.493164}  
         , {11.052246, 50.515136}};  
}  
  
Box boxEast() {  
  return { {11.118164, 50.493164}  
         , {11.140136, 50.515136}};  
}  
  
BoxCart boxEastCart() {  
  return { {11.118164, 50.493164}  
         , {11.140136, 50.515136}};  
}  
  
LineString StuttgartBerlin() {  
    return {Stuttgart(), Berlin()};  
}  
  
LineStringCart StuttgartBerlinCart() {  
    return {StuttgartCart(), BerlinCart()};  
}  
  
int main() {  
  bool const intersectsWest = bg::intersects (StuttgartBerlin(), boxWest());  
  bool const intersectsEast = bg::intersects (StuttgartBerlin(), boxEast());  
  std::vector<LineString> intersectedGeomWest{};  
  std::vector<LineString> intersectedGeomEast{};  
  bg::intersection (StuttgartBerlin(), boxWest(), intersectedGeomWest);  
  bg::intersection (StuttgartBerlin(), boxEast(), intersectedGeomEast);  
  
  std::cout << "West: " << intersectsWest << ", " << intersectedGeomWest.size() << std::endl;  
  std::cout << "East: " << intersectsEast << ", " << intersectedGeomEast.size() << std::endl;  
  
  std::vector<LineStringCart> intersectedGeomCartMiddle{};  
  bool const intersectsCartesianMiddle = bg::intersects (StuttgartBerlinCart(), boxEastCart());  
  bg::intersection (StuttgartBerlinCart(), boxEastCart(), intersectedGeomCartMiddle);  
  std::cout << "Cartesian East: " << intersectsCartesianMiddle << ", " << intersectedGeomCartMiddle.size() << std::endl;  
}  
  
```  
  
With version 1.76.0 (also when incorporating the patch from #851), the result is as follows:  
```  
West: 1, 0  
East: 0, 1  
Cartesian East: 1, 1  
```  
- https://godbolt.org/z/rTn3nKbf9  
  
Please note that in older versions of boost, e.g. 1.64.0, boost will use the Cartesian intersection in any case:  
```  
West: 0, 0  
East: 1, 1  
Cartesian East: 1, 1  
```  
- https://godbolt.org/z/bb3xTxMoK  
  
What I actually expect the program to print is  
```  
West: 1, 1  
East: 0, 0  
Cartesian East: 1, 1  
```  
  
Thank you in advance!

---

## Comment 1

> Username: awulkiew  
> Created at: 2021-06-08 13:29:32 UTC  
> Url: https://github.com/boostorg/geometry/issues/864#issuecomment-856768707  

Thanks! I can reproduce it. This is how it looks like:  
  
![obraz](https://user-images.githubusercontent.com/1226951/121191436-4388e400-c86c-11eb-9916-a8c09f8dd9c6.png)  
  
and up close:  
  
![obraz](https://user-images.githubusercontent.com/1226951/121191501-513e6980-c86c-11eb-8b9b-db77ad3144dd.png)  
  
Blue is the segment densified in cartesian and orange is the segment densified in geographic. The results I get with spherical, andoyer, thomas and vincenty are very close.  
  
So `densify()` and `intersects()` give consistent results. The problem is located in `intersection()` which uses hardcoded cartesian strategy in all coordinate systems:  
https://github.com/boostorg/geometry/blob/develop/include/boost/geometry/algorithms/detail/overlay/intersection_insert.hpp#L682-L685  
  
So yes, your expectations regarding the result are correct.

---

## Comment 2

> Username: awulkiew  
> Created at: 2021-06-08 13:37:56 UTC  
> Updated at: 2021-06-08 13:38:28 UTC  
> Url: https://github.com/boostorg/geometry/issues/864#issuecomment-856775589  

Note that if the boxes are converted to rings/polygons like that:  
```  
using Ring = bg::model::ring<Pt>;  
Ring ringWest, ringEast;  
bg::convert(boxWest, ringWest);  
bg::convert(boxEast, ringEast);  
```  
the result is the expected one. Though there are probably some small differences in coordinates WRT what they should've been if proper boxes were used in calculation.  
  
Have in mind that converting box to ring/polygon is technically incorrect in geographic CS because boxes have different shape. Their horizontal edges are defined by parallels, not geodesics. In this case it works because the boxes are small.

---

## Comment 3

> Username: ghost  
> Created at: 2021-06-23 09:28:28 UTC  
> Url: https://github.com/boostorg/geometry/issues/864#issuecomment-866681677  

> Note that if the boxes are converted to rings/polygons like that:  
>   
> ```  
> using Ring = bg::model::ring<Pt>;  
> Ring ringWest, ringEast;  
> bg::convert(boxWest, ringWest);  
> bg::convert(boxEast, ringEast);  
> ```  
>   
> the result is the expected one. Though there are probably some small differences in coordinates WRT what they should've been if proper boxes were used in calculation.  
>   
> Have in mind that converting box to ring/polygon is technically incorrect in geographic CS because boxes have different shape. Their horizontal edges are defined by parallels, not geodesics. In this case it works because the boxes are small.  
  
Hi, thanks for pointing this out.  
  
Actually, in my case I do want to use precisely the boxes and not the rings.  
  
Can we patch this in `intersection_insert.hpp` only or is it a bigger change?  
  
Thanks and kind regards.

---

## Comment 4

> Username: awulkiew  
> Created at: 2021-06-23 10:20:00 UTC  
> Url: https://github.com/boostorg/geometry/issues/864#issuecomment-866715952  

@mapman63 We have to implement geographic and spherical segment-box intersection strategies.  
  
> Actually, in my case I do want to use precisely the boxes and not the rings.  
  
My point was that this would be the workaround. The custom strategy that we'd have to implement would probably do something similar, i.e. similar math would have to be done in geographic computation. Maybe it'd be slightly simpler in case of calculating the intersection of a geodesic (segment) and meridian (box vertical edge). What do you think @vissarion?  
  
If you'd like to prevent memory allocation here then you could implement your own `static_ring` type, e.g. derived from `std::array`, adapt it to the Ring concept, and convert the box to this `static_ring` by yourself.

---

## Comment 5

> Username: ghost  
> Created at: 2021-06-23 15:06:05 UTC  
> Url: https://github.com/boostorg/geometry/issues/864#issuecomment-866919135  

> My point was that this would be the workaround.  
  
As you pointed out earlier, strictly speaking it is not a workaround if you require high precision (this is the case for me).  
Also larger boxes will lead to even larger errors.

---

## Comment 6

> Username: vissarion  
> Created at: 2021-06-24 09:05:04 UTC  
> Url: https://github.com/boostorg/geometry/issues/864#issuecomment-867470985  

>My point was that this would be the workaround. The custom strategy that we'd have to implement would probably do something similar, i.e. similar math would have to be done in geographic computation. Maybe it'd be slightly simpler in case of calculating the intersection of a geodesic (segment) and meridian (box vertical edge). What do you think @vissarion?  
  
Indeed we have to implement new geographic and spherical segment-box intersection strategies. For meridian case the current intersection algorithm will work as is (since the meridian of a box is a geodesic) but for the box orthogonal edges we need some new formulas. I can prepare a PR on this.

---

## Comment 7

> Username: ghost  
> Created at: 2021-06-25 09:55:10 UTC  
> Url: https://github.com/boostorg/geometry/issues/864#issuecomment-868382828  

@vissarion I'd highly appreciate this for sure. 😄   
  
Please also make sure that it works for the box - ring / polygon / multipolygon combinations.

---

## Comment 8

> Username: vissarion  
> Created at: 2021-09-02 11:06:08 UTC  
> Url: https://github.com/boostorg/geometry/issues/864#issuecomment-911543498  

> Please also make sure that it works for the box - ring / polygon / multipolygon combinations.  
  
For areal geometries (ring/polygon/etc) intersection with a box results in an object that could not yet be represented in boost geometry i.e. it could contain a mixture of (a) geodesic segments coming from box vertical segments (parts of meridians) and polygon geodesic segments, (b) parts of parallels coming from the box orthogonal segments. So the output could be represented by a sequence of points together with an edge information of whether it is a geodesic or a parallel.  
  
One workaround could be to densify  the box (interpolate points along the box segments) and converting it to a ring, then compute the intersection of this ring instead of the box as an approximation. But here some extra work is needed if you need this approximation to cover the original box.    
  
We can propose several workarounds but we have to know more details on the specifications and properties of the output you want to compute.

---

## Comment 9

> Username: ghost  
> Created at: 2021-09-02 11:36:24 UTC  
> Url: https://github.com/boostorg/geometry/issues/864#issuecomment-911570795  

> So the output could be represented by a sequence of points together with an edge information of whether it is a geodesic or a parallel.  
  
I see. Thank you for the clarification! 😄
