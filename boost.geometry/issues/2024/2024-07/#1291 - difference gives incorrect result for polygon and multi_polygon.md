# #1291 - difference gives incorrect result for polygon and multi_polygon [Closed]

> Username: Age-123  
> Created at: 2024-07-10 05:39:45 UTC  
> Updated at: 2024-08-11 11:16:55 UTC  
> Closed at: 2024-08-11 11:16:55 UTC  
> Url: https://github.com/boostorg/geometry/issues/1291  

Program  
```cpp  
#include <boost/geometry.hpp>  
#include <boost/geometry/geometries/point_xy.hpp>  
#include <boost/geometry/geometries/polygon.hpp>  
#include <boost/multiprecision/cpp_dec_float.hpp>  
namespace bg = boost::geometry;  
namespace bm = boost::multiprecision;  
typedef bm::cpp_dec_float_100 float_type;  
typedef bg::model::d2::point_xy<float_type> point_type;  
typedef bg::model::polygon<point_type, false, true> polygon_type;  
  
  
typedef bg::model::multi_polygon<polygon_type> multi_polygon_type;  
  
int main() {  
  polygon_type polygon_layer;  
  bg::read_wkt("POLYGON((0 0, 2 0,2 2, 0 2, 0 0))", polygon_layer);  
  boost::geometry::correct(polygon_layer);  
  multi_polygon_type metal_layer;  
  polygon_type metal_polygon1;  
  bg::read_wkt( "POLYGON((0 0, 1 0, 1 1, 0 1, 0 0))",metal_polygon1);  
  boost::geometry::correct(metal_polygon1);  
  metal_layer.push_back(metal_polygon1);  
  polygon_type metal_polygon2;  
  bg::read_wkt("POLYGON((0 1, 1 1,1 2, 0 2, 0 1))",metal_polygon2);  
  boost::geometry::correct(metal_polygon2);  
  metal_layer.push_back(metal_polygon2);  
  multi_polygon_type result;  
  bg::difference(polygon_layer, metal_layer, result);  
  std::cout << "Difference Result:" << std::endl;  
  for (const auto& poly : result) {  
    std::cout << std::setprecision(10) << bg::wkt(poly) << std::endl;  
  }  
  return 0;  
}  
```  
The answer is POLYGON((0 1,1 1,1 0,2 0,2 2,1 2,0 2,0 1))  
but I think the answer should be POLYGON((1 0,2 0,2 2,1 2,1,0))  
It seems like difference only used to calculate polygon_layer and metal_polygon1, the metal_polygon2 was ignored  
I try to use correct function as you can see in the program, but it is useless  
Look forward to your reply，thanks！

---

## Comment 1

> Username: vissarion  
> Created at: 2024-07-25 13:31:32 UTC  
> Url: https://github.com/boostorg/geometry/issues/1291#issuecomment-2250331852  

Thanks, I can reproduce it. If you reverse the order of the polygons in `metal_layer` the result is correct.   
Looks similar to https://github.com/boostorg/geometry/issues/1222 and https://github.com/boostorg/geometry/issues/1221  
It needs further investigation.

---

## Comment 2

> Username: Age-123  
> Created at: 2024-07-25 13:56:51 UTC  
> Url: https://github.com/boostorg/geometry/issues/1291#issuecomment-2250390081  

> Thanks, I can reproduce it. If you reverse the order of the polygons in `metal_layer` the result is correct.  
> Looks similar to #1222 and #1221  
> It needs further investigation.  
  
thanks for your reply. Does that mean the different orders of adding polygon to multipolygon lead to different results?

---

## Comment 3

> Username: vissarion  
> Created at: 2024-07-26 08:17:50 UTC  
> Url: https://github.com/boostorg/geometry/issues/1291#issuecomment-2252218513  

> > Thanks, I can reproduce it. If you reverse the order of the polygons in `metal_layer` the result is correct.  
> > Looks similar to #1222 and #1221  
> > It needs further investigation.  
>   
> thanks for your reply. Does that mean the different orders of adding polygon to multipolygon lead to different results?  
  
Yes and this should not happen.

---

## Comment 4

> Username: barendgehrels  
> Created at: 2024-07-28 08:18:37 UTC  
> Url: https://github.com/boostorg/geometry/issues/1291#issuecomment-2254390143  

Hi! The description is edited - how can I see the original (without Boost multi precision)? Or was that in the original description already?

---

## Comment 5

> Username: barendgehrels  
> Created at: 2024-07-28 08:26:29 UTC  
> Url: https://github.com/boostorg/geometry/issues/1291#issuecomment-2254392674  

I can reproduce it as well and for me also the different in ordering helps.  
It does not look like a floating point precision error.  
It's not fixed by my concept fix for another issue.

---

## Comment 6

> Username: barendgehrels  
> Created at: 2024-07-28 11:35:48 UTC  
> Url: https://github.com/boostorg/geometry/issues/1291#issuecomment-2254485518  

Additional testing shows that the second input geometry is invalid.  
Ordering changes the result, but it does not give the correct result. The result is wrong in a different way.  
  
`bg::is_valid(poly2)` is false, even after calling `correct`. They don't self overlap.  
  
Can we close this as not-a-bug?

---

## Comment 7

> Username: barendgehrels  
> Created at: 2024-07-28 12:15:32 UTC  
> Url: https://github.com/boostorg/geometry/issues/1291#issuecomment-2254497948  

BTW the reason that I checked it again is that I have a concept fix for another recent issue and wanted to check if it would fix this as well.

---

## Comment 8

> Username: Age-123  
> Created at: 2024-07-28 12:45:38 UTC  
> Url: https://github.com/boostorg/geometry/issues/1291#issuecomment-2254506889  

> Additional testing shows that the second input geometry is invalid. Ordering changes the result, but it does not give the correct result. The result is wrong in a different way.  
>   
> `bg::is_valid(poly2)` is false, even after calling `correct`. They don't self overlap.  
>   
> Can we close this as not-a-bug?  
  
Thanks for your reply,  but if I only add poly2 or poly1 to the multipolygon, the results both are true, so do you mean when I add poly1 and poly2 to the multipolygon, the poly2 will be invalid? I think poly1 and poly2 are similar

---

## Comment 9

> Username: Age-123  
> Created at: 2024-07-28 12:49:12 UTC  
> Updated at: 2024-07-28 12:51:55 UTC  
> Url: https://github.com/boostorg/geometry/issues/1291#issuecomment-2254507884  

> Hi! The description is edited - how can I see the original (without Boost multi precision)? Or was that in the original description already?  
  
The reason why I use Boost multi precision is the precision of double or float is not enough when I want to use _intersection_ to calculate the intersecting linestring of two closed linestrings

---

## Comment 10

> Username: Age-123  
> Created at: 2024-07-28 13:50:06 UTC  
> Updated at: 2024-07-28 14:07:20 UTC  
> Url: https://github.com/boostorg/geometry/issues/1291#issuecomment-2254526168  

> Additional testing shows that the second input geometry is invalid. Ordering changes the result, but it does not give the correct result. The result is wrong in a different way.  
>   
> `bg::is_valid(poly2)` is false, even after calling `correct`. They don't self overlap.  
>   
> Can we close this as not-a-bug?  
  
I  change the order of poly1 and poly2, the result is “POLYGON ((1 2,1 1,1 0,2 0,2 2,1 2))”, it seems like the top of poly1 and the bottom of poly2 are not completely overlapping(because  the coordinates(1,1)),  but I think they should be completely overlapping, and I use bg::is_valid(poly2),the result is true, maybe our poly2 is different, the boost version I use is 1.85

---

## Comment 11

> Username: barendgehrels  
> Created at: 2024-07-28 19:18:18 UTC  
> Url: https://github.com/boostorg/geometry/issues/1291#issuecomment-2254617884  

Good point, I renamed them in my local verification. So then please apply:  
`std::cout << "Valid: " << std::boolalpha << bg::is_valid(metal_layer) << std::endl;`  
and it will show it is invalid (in either order).

---

## Comment 12

> Username: barendgehrels  
> Created at: 2024-07-28 19:58:09 UTC  
> Url: https://github.com/boostorg/geometry/issues/1291#issuecomment-2254627719  

> Thanks for your reply, but if I only add poly2 or poly1 to the multipolygon, the results both are true, so do you mean when I add poly1 and poly2 to the multipolygon, the poly2 will be invalid? I think poly1 and poly2 are similar  
  
Yes but their combination is invalid
