# #1293 - Difference of polygons is incorrectly empty [Closed]

> Username: janmoltz  
> Created at: 2024-07-25 09:29:43 UTC  
> Updated at: 2024-08-08 13:25:00 UTC  
> Closed at: 2024-08-08 13:25:00 UTC  
> Url: https://github.com/boostorg/geometry/issues/1293  

The `difference` method for polygons returns an incorrect empty result in some cases. This is an example:  
```  
#include <boost/geometry.hpp>  
#include <boost/geometry/geometries/point_xy.hpp>  
#include <boost/geometry/geometries/polygon.hpp>  
#include <boost/geometry/geometries/multi_polygon.hpp>  
typedef boost::geometry::model::d2::point_xy<double> BoostPoint;  
typedef boost::geometry::model::polygon<BoostPoint> BoostPolygon;  
typedef boost::geometry::model::multi_polygon<BoostPolygon> BoostMultiPolygon;  
  
BoostPolygon polygon1;  
boost::geometry::append(polygon1, BoostPoint(13.90250403054817, -108.7493905903629)); // when adding this point to polygon1 as the last point, it works  
boost::geometry::append(polygon1, BoostPoint(18.1717071533203, -109.6161492466927)); // very similar to first point of polygon2; when changing coordinate to, for example, 18.1717071534203, it works  
boost::geometry::append(polygon1, BoostPoint(20.38385009765624, -109.6161492466927));  
boost::geometry::append(polygon1, BoostPoint(13.74227594050434, -109.9162293522026));  
boost::geometry::append(polygon1, BoostPoint(13.80791011645497, -109.3242516085326));  
boost::geometry::correct(polygon1);  
BoostPolygon polygon2;  
boost::geometry::append(polygon2, BoostPoint(18.17170715332031, -109.6161492466927));  
boost::geometry::append(polygon2, BoostPoint(19.60749526774088, -109.5220427274138));  
boost::geometry::append(polygon2, BoostPoint(11.19696044921875, -108.1424287761621));  
boost::geometry::append(polygon2, BoostPoint(12.48744269320276, -108.7788241542253));  
boost::geometry::append(polygon2, BoostPoint(13.84995095309101, -109.2413333582132));  
boost::geometry::append(polygon2, BoostPoint(15.26117233479817, -109.5220427274138));  
boost::geometry::append(polygon2, BoostPoint(16.69696044921874, -109.6161492466927));  
boost::geometry::correct(polygon2);  
BoostMultiPolygon result;  
boost::geometry::difference(polygon1, polygon2, result);  
std::cout << result.size() << std::endl; // 0, should be 1  
```  
The polygons look like this:  
<img width="1036" alt="image" src="https://github.com/user-attachments/assets/c65b1904-9349-47ee-b83c-0bb06fe2e75a">  
They intersect and clearly should have a non-empty difference. The `intersection` method returns the correct result. The comments in the code show two very small modifications that solve the problem.  
I observed the bug with boost 1.84 and 1.85. It did not occur in 1.81. I was not able to try the versions between.

---

## Comment 1

> Username: vissarion  
> Created at: 2024-07-25 09:47:06 UTC  
> Url: https://github.com/boostorg/geometry/issues/1293#issuecomment-2249922984  

Thanks, I can reproduce it. As with many similar issues by increasing precision (i.e. `long double` instead of `double`) the result is correct.

---

## Comment 2

> Username: hmeine  
> Created at: 2024-07-25 09:56:45 UTC  
> Updated at: 2024-07-25 10:09:56 UTC  
> Url: https://github.com/boostorg/geometry/issues/1293#issuecomment-2249940691  

Does the fact that the order of the points / segments matters (i.e., rotating the first point of polygon1 to the end fixes the problem) suggest that there might be an algorithmic solution as well? Or is this an inevitable edge case?

---

## Comment 3

> Username: vissarion  
> Created at: 2024-07-25 10:39:02 UTC  
> Url: https://github.com/boostorg/geometry/issues/1293#issuecomment-2250018817  

There could be an algorithmic solution to this but I have to look at the issue in more detail.

---

## Comment 4

> Username: janmoltz  
> Created at: 2024-07-25 11:46:46 UTC  
> Url: https://github.com/boostorg/geometry/issues/1293#issuecomment-2250132943  

Thank you for your quick answer. Unfortunately using `long double` does not help me because I am on Visual Studio. Therefore I would be very interested in an algorithmic solution.

---

## Comment 5

> Username: barendgehrels  
> Created at: 2024-07-28 07:55:39 UTC  
> Updated at: 2024-07-28 07:58:16 UTC  
> Url: https://github.com/boostorg/geometry/issues/1293#issuecomment-2254382864  

I can reproduce it as well (MacOS, clang).  
The concept fix I have for other issues unfortunately does not work for this issue yet.  
On (my) mac `long double` does not help either. Only using `float` works for this case, but that is no real solution. But it indicates that it is another floating point precision issue.

---

## Comment 6

> Username: barendgehrels  
> Created at: 2024-07-29 09:44:28 UTC  
> Url: https://github.com/boostorg/geometry/issues/1293#issuecomment-2255476490  

I've a working fix (also fixing #1294 an #1295) and will make a PR on Wednesday
