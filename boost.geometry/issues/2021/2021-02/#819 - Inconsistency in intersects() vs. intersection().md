# #819 - Inconsistency in intersects() vs. intersection() [Closed]

> Username: fhw72  
> Created at: 2021-02-23 19:08:54 UTC  
> Updated at: 2021-02-24 10:50:45 UTC  
> Closed at: 2021-02-23 19:56:58 UTC  
> Url: https://github.com/boostorg/geometry/issues/819  

In my application I noticed an inconsistency in behaviour of:  
  
- intersects() vs.  
- intersection()  
  
The problem is that intersects() returns true but the subsequent  
call to intersection() yields an empty result.  
  
The problem can be reproduced with the following code:  
  
```  
#include <iostream>  
#include <boost/geometry.hpp>  
  
namespace bg = boost::geometry;  
using point_t = bg::model::d2::point_xy<double>;  
using polygon_t = bg::model::polygon<point_t>;  
using mpolygon_t = bg::model::multi_polygon<polygon_t>;  
  
int main()  
{  
    polygon_t poly1, poly2;  
    mpolygon_t poly3, poly4, poly5;  
  
    bg::read_wkt("POLYGON(("  
        "12227.0 4967.0000000000009, 12238.0 4967.0000000000009, "  
        "12238.0 4813.0000000000009, 12227.0 4813.0000000000009, "  
        "12227.0 4967.0000000000009))", poly1);  
    bg::read_wkt("POLYGON(("  
        "12254.0 4947.0, 12219.0 4982.0, 12219.0 5020.0, 12254.0 5055.0, "  
        "12261.0 5055.0, 12263.0 5055.0, 12283.0 5055.0, 12283.0 4947.0, "  
        "12263.0 4947.0, 12261.0 4947.0, 12254.0 4947.0))", poly2);  
  
    bg::intersection(poly1, poly2, poly3);  
    bg::difference(poly1, poly3[0], poly4);  
  
    // b0 = true, b1 = false  
    bool b0 = bg::intersects(poly2, poly4[0]);  
    bg::intersection(poly2, poly4, poly5);  
    bool b1 = (poly5.size() != 0);  
  
    std::cout << b0 << b1 << std::endl;  
    return 1;  
}  
  
```

---

## Comment 1

> Username: mloskot  
> Created at: 2021-02-23 19:56:58 UTC  
> Url: https://github.com/boostorg/geometry/issues/819#issuecomment-784470663  

> ```  
> bg::intersection(poly2, poly4, poly5);  
> ```  
  
```cpp  
using linestring_t = bg::model::linestring<point_t>;  
linestring_t int1;  
bg::intersection(poly2, poly4, int1);  
std::cout << "int1: " << bg::wkt(int1) << std::endl;  
```  
  
Output:  
  
```  
int1: LINESTRING(12238 4963,12234 4967)  
```

---

## Comment 2

> Username: awulkiew  
> Created at: 2021-02-23 21:00:03 UTC  
> Url: https://github.com/boostorg/geometry/issues/819#issuecomment-784509099  

@mloskot You should've probably passed `multi_linestring`. I would be possible to have multiple linestrings/segments in the output.

---

## Comment 3

> Username: mloskot  
> Created at: 2021-02-23 21:10:09 UTC  
> Url: https://github.com/boostorg/geometry/issues/819#issuecomment-784514831  

@awulkiew There is number of possibilities. I just illustrated for this particular input.

---

## Comment 4

> Username: fhw72  
> Created at: 2021-02-24 07:29:11 UTC  
> Updated at: 2021-02-24 09:20:03 UTC  
> Url: https://github.com/boostorg/geometry/issues/819#issuecomment-784858672  

@mloskot I understand that bg::intersection() returns a (multi_)linestring but the real question is:  
  
Shouldn't  
`bool b0 = bg::intersects(poly2, poly4[0]);`  
return false?  
  
In the code I have poly1 and poly2 and their intersection poly3:  
`bg::intersection(poly1, poly2, poly3);`  
  
This intersection is removed from poly1 (result: poly4):  
`bg::difference(poly1, poly3[0], poly4);`  
  
That means that there shouldn't be any intersection between poly2 and poly4 left?  
(If I change the coordinates in poly1 from 4967.0000000000009 to 4967.0 etc.) intersects  
return false as expected.

---

## Comment 5

> Username: mloskot  
> Created at: 2021-02-24 09:50:24 UTC  
> Updated at: 2021-02-24 09:52:24 UTC  
> Url: https://github.com/boostorg/geometry/issues/819#issuecomment-784951603  

> Shouldn't `bool b0 = bg::intersects(poly2, poly4[0]);` return false?  
  
AFAICT, the two touch each other and the `touches` relation implies intersection  
  
![image](https://user-images.githubusercontent.com/80741/108981622-306f5100-768d-11eb-80d5-2ac72c39a41e.png)  
  
> This intersection is removed from `poly1` (result: `poly4`): `bg::difference(poly1, poly3[0], poly4);`  
  
Yes, but the operation can not, topologically, remove the [boundary, just the interior (residue)](https://en.wikipedia.org/wiki/Boundary_(topology)).  
  
FYI, I've also confirmed the result from https://github.com/boostorg/geometry/issues/819#issuecomment-784470663 using https://github.com/libgeos/geos  
  
> If I change the coordinates in poly1 from 4967.0000000000009 to 4967.0 etc.  
  
Welcome the issues of the float-point robustness?

---

## Comment 6

> Username: fhw72  
> Created at: 2021-02-24 09:55:47 UTC  
> Url: https://github.com/boostorg/geometry/issues/819#issuecomment-784954684  

> Welcome the issues of the float-point robustness?  
  
Can you shed some light on how to deal with it? I didn't find any description of the  
  
BOOST_GEOMETRY_NO_ROBUSTNESS  
  
macro... maybe I've overseen something?

---

## Comment 7

> Username: vissarion  
> Created at: 2021-02-24 10:00:21 UTC  
> Url: https://github.com/boostorg/geometry/issues/819#issuecomment-784957518  

>That means that there shouldn't be any intersection between poly2 and poly4 left?  
(If I change the coordinates in poly1 from 4967.0000000000009 to 4967.0 etc.) intersects  
return false as expected.  
  
This is not clear at all. Do you change the coordinates before computing the `poly3` and `poly4`? The intersection polygon (`poly3`) seems large enough to suffer from floating point robustness.

---

## Comment 8

> Username: fhw72  
> Created at: 2021-02-24 10:03:07 UTC  
> Updated at: 2021-02-24 10:10:53 UTC  
> Url: https://github.com/boostorg/geometry/issues/819#issuecomment-784959325  

@vissarion Yes... I mean if I change the poly1 coordinates BEFORE computing poly3 and poly4 to  
  
```  
bg::read_wkt("POLYGON(("  
        "12227.0 4967.0000000000000, 12238.0 4967.0000000000000, "  
        "12238.0 4813.0000000000000, 12227.0 4813.0000000000000, "  
        "12227.0 4967.0000000000000))", poly1);  
```  
  
bg::intersect() returns false (though poly2 and poly4 are still touching each other, right?)

---

## Comment 9

> Username: vissarion  
> Created at: 2021-02-24 10:13:14 UTC  
> Url: https://github.com/boostorg/geometry/issues/819#issuecomment-784965902  

Then I cannot reproduce this behavior on my side. I am still getting the following (blue: poly1, green: poly2, red: the intersection of poly2 and poly4)   
![intersection_example](https://user-images.githubusercontent.com/3660366/108985025-3cf9a680-7699-11eb-86df-19bc730a4f40.png)

---

## Comment 10

> Username: fhw72  
> Created at: 2021-02-24 10:24:09 UTC  
> Url: https://github.com/boostorg/geometry/issues/819#issuecomment-784972881  

Oh... the penny has fallen now. The intersection is still there it's just a line string and  
  
the sample code I used to demonstrate the behavior was slightly different than the code in my  
application.

---

## Comment 11

> Username: mloskot  
> Created at: 2021-02-24 10:49:30 UTC  
> Updated at: 2021-02-24 10:50:45 UTC  
> Url: https://github.com/boostorg/geometry/issues/819#issuecomment-784988228  

> The intersection is still there it's just a line string  
  
Yes! Because it is part of the polygon(s) boundary - no intersection there takes part of the interior(s)
