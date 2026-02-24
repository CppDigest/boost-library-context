# #1290 - The result of the difference(A, B, C) intersects() with B but neither overlaps() nor touches(). [Open]

> Username: Zeratul-Aiur  
> Created at: 2024-07-04 03:36:41 UTC  
> Updated at: 2024-07-28 12:16:52 UTC  
> Url: https://github.com/boostorg/geometry/issues/1290  

Hi,  
  
I obtained the difference between the two rings and found that the result intersects with the original ring, but it neither overlaps nor touches it. I think in the case of intersection, `overlaps(A, B) == !touches(A, B)`.  
  
example code:  
  
```cpp  
TEST_CASE("高精度浮点图形", "[geometry][boost]") {  
  using Point = bg::model::point<double, 2, bg::cs::cartesian>;  
  using Ring = bg::model::ring<Point, false, false>;  
  using Polygon = bg::model::polygon<Point, false, false>;  
  using MultiPolygon = bg::model::multi_polygon<Polygon>;  
  
  const Ring ring1{{255.999985, 255.999954},  
                   {376.0, 223.846039},  
                   {376.0, 421.0},  
                   {368.0, 421.0},  
                   {368.0, 420.0},  
                   {350.685455, 420.0}};  
  const Ring ring2{{437.019287, -57.5347023},  
                   {503.276947, 189.742279},  
                   {8.72293091, 322.257660},  
                   {-57.5347290, 74.9806824}};  
  
  REQUIRE(bg::intersects(ring1, ring2));  
  
  MultiPolygon result;  
  
  REQUIRE_NOTHROW(bg::difference(ring1, ring2, result));  
  REQUIRE(bg::intersects(ring2, result));  
  REQUIRE_FALSE(bg::overlaps(ring2, result));  
  REQUIRE_FALSE(bg::equals(ring2, result));  
  REQUIRE_FALSE(bg::touches(ring2, result));  
}  
```  
  
![image](https://github.com/boostorg/geometry/assets/74289088/ec417bdc-ad7d-49be-91b0-ea21df3e4eb4)  
![image](https://github.com/boostorg/geometry/assets/74289088/9c135dd9-3c0d-4ace-a911-4d675802226e)  
  
version: 1.85.0

---

## Comment 1

> Username: vissarion  
> Created at: 2024-07-04 11:37:50 UTC  
> Url: https://github.com/boostorg/geometry/issues/1290#issuecomment-2208752966  

Thanks for opening this issue, I can reproduce it. It seems like a numerical issue. With more precision (`long double`) boost geometry computes the desired result, i.e. ring2, result do not intersect nor overlap nor touch.

---

## Comment 2

> Username: Zeratul-Aiur  
> Created at: 2024-07-08 02:47:59 UTC  
> Url: https://github.com/boostorg/geometry/issues/1290#issuecomment-2212877810  

What's a good way to handle getting higher precision results from calculations performed with lower precision data?

---

## Comment 3

> Username: vissarion  
> Created at: 2024-07-08 10:41:38 UTC  
> Url: https://github.com/boostorg/geometry/issues/1290#issuecomment-2213658993  

As a user you have to create your input geometry with higher precision e.g.   
`using Point = bg::model::point<long double, 2, bg::cs::cartesian>;` or use a more precise data type e.g. from boost multiprecision.

---

## Comment 4

> Username: barendgehrels  
> Created at: 2024-07-28 09:35:41 UTC  
> Url: https://github.com/boostorg/geometry/issues/1290#issuecomment-2254451371  

### My version  
```  
#include <boost/geometry.hpp>  
#include <boost/geometry/geometries/geometries.hpp>  
#include <iostream>  
#include <iomanip>  
  
namespace bg = boost::geometry;  
  
template <typename T>  
void test()  
{  
    using point_t = bg::model::point<T, 2, bg::cs::cartesian>;  
    using ring_t = bg::model::ring<point_t, false, false>;  
    using polygon_t = bg::model::polygon<point_t, false, false>;  
    using mp_t = bg::model::multi_polygon<polygon_t>;  
  
 ring_t ring1{{255.999985, 255.999954},  
                   {376.0, 223.846039},  
                   {376.0, 421.0},  
                   {368.0, 421.0},  
                   {368.0, 420.0},  
                   {350.685455, 420.0}};  
   ring_t ring2{{437.019287, -57.5347023},  
                   {503.276947, 189.742279},  
                   {8.72293091, 322.257660},  
                   {-57.5347290, 74.9806824}};      
  
    bg::correct(ring1);  
    bg::correct(ring2);  
  
    mp_t diff, inters;  
    bg::difference(ring1, ring2, diff);  
    bg::intersection(ring1, ring2, inters);  
  
    std::cout << std::setprecision(12) << std::boolalpha;  
    std::cout   
        << "Type: " << typeid(T).name()  
        << " a: " << bg::area(ring1)  
        << " b: " << bg::area(ring2)  
        << " d: " << bg::area(diff)  
        << " i: " << bg::area(inters)  
        << " b-a: " << bg::area(ring2) - bg::area(ring1)  
        << " overlaps: " << bg::overlaps(diff, ring2)  
        << " intersects: " << bg::intersects(diff, ring2)  
        << " touch: " << bg::touches(diff, ring2)  
        << std::endl;  
}  
  
int main() {  
    test<float>();  
    test<double>();  
    test<long double>();  
}  
```  
  
### My output  
```  
Type: f a: 13853.0324104 b: 131071.982442 d: 13853.0324104 i: 0 b-a: 117218.950032 intersects: true overlaps: true touch: false  
Type: d a: 13853.0324034 b: 131071.982438 d: 13853.0320132 i: 0.000390214508673 b-a: 117218.950035 intersects: false overlaps: false touch: false  
Type: e a: 13853.0324034 b: 131071.982438 d: 13853.0320132 i: 0.000390214508673 b-a: 117218.950035 intersects: false overlaps: false touch: false  
  
```  
  
So yes, it looks like I can reproduce it as well (note that on my mac there is no difference in double/long double types).  
The difference/intersections themselves look OK, is that correct? (I didn't visualize it)  
So there must be an error in the boolean predicates.

---

## Comment 5

> Username: barendgehrels  
> Created at: 2024-07-28 12:16:51 UTC  
> Url: https://github.com/boostorg/geometry/issues/1290#issuecomment-2254498385  

BTW the reason that I checked it again is that I have a concept fix for another recent issue and wanted to check if it would fix this as well. But that is related to the spatial set operations, and not to the predicates.
