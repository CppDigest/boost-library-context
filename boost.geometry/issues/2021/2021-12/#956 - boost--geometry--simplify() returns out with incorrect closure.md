# #956 - boost::geometry::simplify() returns out with incorrect closure [Closed]

> Username: Zeratul-Aiur  
> Created at: 2021-12-31 03:11:03 UTC  
> Updated at: 2022-01-25 18:55:13 UTC  
> Closed at: 2022-01-19 23:37:41 UTC  
> Url: https://github.com/boostorg/geometry/issues/956  

I create a ring with counterclockwise and open points.When calculating the simplify I found that the result did not detect closure_selector.  
  
example code:  
```  
#include <boost/geometry.hpp>  
#include <iostream>  
  
using namespace boost::geometry;  
  
int main() {  
  using Point = model::point<float, 2, cs::cartesian>;  
  using Ring = model::ring<Point, false, false>;  
  Ring ring{{0, 0}, {0.4, 0}, {0.4, 0.4}};  
  Ring result;  
  simplify(ring, result, 0);  
  std::cout << wkt(result) << std::endl;  
}  
```  
Output : POLYGON((0.4 0.4,0 0,0.4 0,0.4 0.4))  
I would expect to get : POLYGON((0.4 0.4,0 0,0.4 0))  
  
I think the problem is here:  
```  
// In simplify.hpp  
  
std::rotate_copy(boost::begin(ring), range::pos(ring, index),  
                        boost::end(ring), rotated.begin());  
  
// Close the rotated copy  
rotated.push_back(range::at(ring, index));  
  
simplify_range<0>::apply(rotated, out, max_distance, impl, strategies);  
```

---

## Comment 1

> Username: yashdabhade1  
> Created at: 2022-01-16 19:04:37 UTC  
> Url: https://github.com/boostorg/geometry/issues/956#issuecomment-1013933325  

A polygon is defined in aw ay where the first and last points are same  
So i feel that the output is correct

---

## Comment 2

> Username: awulkiew  
> Created at: 2022-01-18 16:41:02 UTC  
> Updated at: 2022-01-18 16:44:43 UTC  
> Url: https://github.com/boostorg/geometry/issues/956#issuecomment-1015596373  

Thanks for the report! I was able to reproduce it and prepare a fix.  
  
@yashdabhade1 the Ring/Polygon in the example is defined as counter-clockwise and opened so there should be no closing point in the output.  
  
Side note. With `bg::wkt(result)` the text representation for this ring has 3 points but if it was a polygon it would have 4 points. This doesn't mean that the polygon would be closed. Current implementation of `bg::wkt()` works this way. So if you want to get the number of points that are in the geometry consider using e.g. `bg::wkt_manipulator<Geometry>(result, false)` or `bg::dsv(result)` instead.

---

## Comment 3

> Username: awulkiew  
> Created at: 2022-01-18 16:44:47 UTC  
> Updated at: 2022-01-18 18:14:37 UTC  
> Url: https://github.com/boostorg/geometry/issues/956#issuecomment-1015601798  

@barendgehrels @mloskot @vissarion Should the behavior of `bg::wkt()` be changed for rings and made consistent with polygons?

---

## Comment 4

> Username: vissarion  
> Created at: 2022-01-19 08:17:14 UTC  
> Updated at: 2022-01-19 08:17:34 UTC  
> Url: https://github.com/boostorg/geometry/issues/956#issuecomment-1016188115  

> @barendgehrels @mloskot @vissarion Should the behavior of `bg::wkt()` be changed for rings and made consistent with polygons?  
  
In general I am in favor of consistency since a ring is semantically a polygon and the current behavior of `wkt()` is confusing. But I have no clue about the effect to current users since this is a breaking change.   
  
In conclusion +1 from me to change the behavior.

---

## Comment 5

> Username: mloskot  
> Created at: 2022-01-19 09:22:18 UTC  
> Url: https://github.com/boostorg/geometry/issues/956#issuecomment-1016239651  

> In conclusion +1 from me to change the behavior.  
  
+1 I second @vissarion opinion

---

## Comment 6

> Username: awulkiew  
> Created at: 2022-01-19 11:12:08 UTC  
> Url: https://github.com/boostorg/geometry/issues/956#issuecomment-1016348643  

It seems I already asked a question about this but is was left unanswered:  
https://github.com/boostorg/geometry/commit/0f098948b2635e18925c935b7545c707e95ef4bd#diff-d9e85ab51605913771ebd3fac715d4a68cdad4ef410f4b01b94e327682f2d2ba  
@barendgehrels do you remember what breaking change was it?  
Was this about failing tests that would have to be modified otherwise?  
  
It was a followup change after this one which may have something to do with correct_closure (I asked about this too):  
https://github.com/boostorg/geometry/commit/917b18e66ca29eaff204b7cb668ea3f8e09ba8ce#diff-d9e85ab51605913771ebd3fac715d4a68cdad4ef410f4b01b94e327682f2d2ba  
  
There is an additional commit with correct_closure, all three made Oct 19, 2017:  
https://github.com/boostorg/geometry/commits/develop/include/boost/geometry/io/wkt/write.hpp

---

## Comment 7

> Username: barendgehrels  
> Created at: 2022-01-25 18:55:13 UTC  
> Url: https://github.com/boostorg/geometry/issues/956#issuecomment-1021506021  

Hi @awulkiew , I don't remember by head, I will look tomorrow into it
