# #1284 - Negative buffer on MultiPolygon loses precision in area calculation [Closed]

> Username: sghofrany  
> Created at: 2024-05-16 15:59:40 UTC  
> Updated at: 2024-11-25 18:34:22 UTC  
> Closed at: 2024-11-25 18:32:55 UTC  
> Url: https://github.com/boostorg/geometry/issues/1284  

Using Boost: 1.84  
C++ 17  
  
I am attempting to go from a Point to a Polygon using a positive buffer value and then using the resulting Polygon to generate a smaller Polygon with a negative buffer. However, the resulting smaller polygon has a noticeable area difference than what I'd expect when calculating the area by hand. Here is the example I am running:  
  
Steps:  
1) Point -> Buffer with a value of 10 -> produces Polygon A  
2) Polygon A -> Buffer with a value of -3 -> Produces Polygon B  
  
Very accurate:  
  
```  
Expected Area for Step 1: 312.56671980047457  
Actual Area for Step 1:  312.566719800474857038  
```  
  
Not so accurate:  
  
```  
Expected Area for Step 2: 153.93022477684056  
Actual Area for Step 2:  153.897440054815035637  
```  
  
Although the area difference doesn't seem that different in Step 2, you have to realize that is mainly the result of the high pointsPerCircle value. If I lower that to say 36 instead, we start seeing a difference in a meter or more in the output.  
  
Buffer Code:  
```cpp  
int pointsPerCircle = 360;  
boost::geometry::strategy::buffer::distance_symmetric<double> distanceStrategy(buffer);  
boost::geometry::strategy::buffer::side_straight sideStrategy;  
boost::geometry::strategy::buffer::join_round joinStrategy(pointsPerCircle);  
boost::geometry::strategy::buffer::end_round endStrategyRound(pointsPerCircle);  
boost::geometry::strategy::buffer::point_circle circleStrategy(pointsPerCircle);  
  
MultiPolygonType geometry;  
MultiPolygonType result;  
boost::geometry::buffer(geometry, result, distanceStrategy, sideStrategy, joinStrategy, endStrategyRound, circleStrategy);  
```  
  
Area code:  
```cpp  
long double area = boost::geometry::area(result);  
```  
  
Is the expected behavior for negative buffers, or am I doing something wrong?

---

## Comment 1

> Username: vissarion  
> Created at: 2024-05-17 14:00:28 UTC  
> Url: https://github.com/boostorg/geometry/issues/1284#issuecomment-2117677061  

Thanks for opening this issue. Could you please provide a complete working example?

---

## Comment 2

> Username: sghofrany  
> Created at: 2024-05-17 14:02:46 UTC  
> Url: https://github.com/boostorg/geometry/issues/1284#issuecomment-2117681222  

> Thanks for opening this issue. Could you please provide a complete working example?  
  
I'll try, does it need to be in repo or can I just share the code here?

---

## Comment 3

> Username: vissarion  
> Created at: 2024-05-17 14:04:51 UTC  
> Url: https://github.com/boostorg/geometry/issues/1284#issuecomment-2117685349  

Just paste the code here please. See for example, https://github.com/boostorg/geometry/issues/1238

---

## Comment 4

> Username: sghofrany  
> Created at: 2024-05-17 14:27:10 UTC  
> Url: https://github.com/boostorg/geometry/issues/1284#issuecomment-2117733541  

Here is a basic example:  
```cpp  
  
// Calculate the expected area of the buffered geometry using Area of Triangle formula.  
// https://www.cuemath.com/measurement/area-of-triangle-with-2-sides-and-included-angle-sas/  
inline double expectedArea(double bufferAmount, double pointsPerCircle) {  
  double degreesBetweenPoints = 360.0 / pointsPerCircle;  
  double rad = (boost::math::constants::pi<double>()/180) * degreesBetweenPoints;  
  double expectedArea = bufferAmount * bufferAmount * sin(rad) * 0.5 * pointsPerCircle;  
  
  return expectedArea;  
}  
  
int main() {  
  using PointType = boost::geometry::model::d2::point_xy<double>;  
  using PolygonType = boost::geometry::model::polygon<PointType>;  
  using MultiPolygonType = boost::geometry::model::multi_polygon<PolygonType>;  
  
  double bufferAmount = 10;  
  int pointsPerCircle = 36;  
  
  boost::geometry::strategy::buffer::side_straight sideStrategy;  
  boost::geometry::strategy::buffer::join_round joinStrategy(pointsPerCircle);  
  boost::geometry::strategy::buffer::end_round endStrategyRound(pointsPerCircle);  
  boost::geometry::strategy::buffer::point_circle circleStrategy(pointsPerCircle);  
  
  boost::geometry::strategy::buffer::distance_symmetric<double> distanceStrategy(bufferAmount);  
  
  string wkt = "POINT (0 0)";  
  
  MultiPolygonType result;  
  auto point = boost::geometry::from_wkt<PointType>(wkt);  
  
  // Buffer a point to a MultiPolygon  
  boost::geometry::buffer(point,  
                          result,  
                          distanceStrategy,  
                          sideStrategy,  
                          joinStrategy,  
                          endStrategyRound,  
                          circleStrategy);  
  
  std::cout << boost::geometry::area(result) << "\n";  
  std::cout << expectedArea(bufferAmount, static_cast<double>(pointsPerCircle)) << "\n";  
  
  double negativeBuffer = -3;  
  MultiPolygonType negativeResult;  
  
  boost::geometry::strategy::buffer::distance_symmetric<double> distanceStrategyNegative(negativeBuffer);  
  
  // Buffer the result of the previous step by -3  
  boost::geometry::buffer(result,  
                          negativeResult,  
                          distanceStrategyNegative,  
                          sideStrategy,  
                          joinStrategy,  
                          endStrategyRound,  
                          circleStrategy);  
  
  std::cout << "Negative Buffer:" << boost::geometry::area(negativeResult) << "\n";  
  // we use (bufferAmount + negativeBuffer) because we need the updated radius (10 + -3 = 7)  
  std::cout << "Negative Buffer:" << expectedArea((bufferAmount + negativeBuffer), static_cast<double>(pointsPerCircle)) << "\n";  
  
return 0;  
}  
```

---

## Comment 5

> Username: sghofrany  
> Created at: 2024-05-22 14:44:35 UTC  
> Url: https://github.com/boostorg/geometry/issues/1284#issuecomment-2124986388  

@vissarion I was wondering if you had a chance to take a look at this, thanks

---

## Comment 6

> Username: barendgehrels  
> Created at: 2024-11-25 18:32:55 UTC  
> Url: https://github.com/boostorg/geometry/issues/1284#issuecomment-2498758202  

I get, with an adapted variant (where I increase the number of points per circle):  
```  
314.159  
314.159  
Detected:153.913  
Expected:153.938  
```  
  
Which looks good to me. In positive buffers, automatically more points are used. Therefore the difference in precision.  
If you use negative buffers, points are closer together so might get a difference, depending on the number of points you use.  
Closing as "not a bug"  
  
My code, now including the necessary `includes` and avoiding using namespace `std`:  
```  
#include <boost/geometry.hpp>  
#include <boost/geometry/geometries/geometries.hpp>  
#include <iostream>  
#include <limits>  
  
  
// Calculate the expected area of the buffered geometry using Area of Triangle formula.  
// https://www.cuemath.com/measurement/area-of-triangle-with-2-sides-and-included-angle-sas/  
inline double expectedArea(double bufferAmount, double pointsPerCircle) {  
  double degreesBetweenPoints = 360.0 / pointsPerCircle;  
  double rad = (boost::math::constants::pi<double>()/180) * degreesBetweenPoints;  
  double expectedArea = bufferAmount * bufferAmount * sin(rad) * 0.5 * pointsPerCircle;  
  
  return expectedArea;  
}  
  
int main() {  
  using PointType = boost::geometry::model::d2::point_xy<double>;  
  using PolygonType = boost::geometry::model::polygon<PointType>;  
  using MultiPolygonType = boost::geometry::model::multi_polygon<PolygonType>;  
  
  double bufferAmount = 10.0;  
  int pointsPerCircle = 36000;  
  
  boost::geometry::strategy::buffer::side_straight sideStrategy;  
  boost::geometry::strategy::buffer::join_round joinStrategy(pointsPerCircle);  
  boost::geometry::strategy::buffer::end_round endStrategyRound(pointsPerCircle);  
  boost::geometry::strategy::buffer::point_circle circleStrategy(pointsPerCircle);  
  
  boost::geometry::strategy::buffer::distance_symmetric<double> distanceStrategy(bufferAmount);  
  
  PointType point{0.0, 0.0};  
  MultiPolygonType result;  
  
  // Buffer a point to a MultiPolygon  
  boost::geometry::buffer(point,  
                          result,  
                          distanceStrategy,  
                          sideStrategy,  
                          joinStrategy,  
                          endStrategyRound,  
                          circleStrategy);  
  
  std::cout << boost::geometry::area(result) << "\n";  
  std::cout << expectedArea(bufferAmount, static_cast<double>(pointsPerCircle)) << "\n";  
  
  double negativeBuffer = -3;  
  MultiPolygonType negativeResult;  
  
  boost::geometry::strategy::buffer::distance_symmetric<double> distanceStrategyNegative(negativeBuffer);  
  
  // Buffer the result of the previous step by -3  
  boost::geometry::buffer(result,  
                          negativeResult,  
                          distanceStrategyNegative,  
                          sideStrategy,  
                          joinStrategy,  
                          endStrategyRound,  
                          circleStrategy);  
  
  std::cout << "Detected:" << boost::geometry::area(negativeResult) << "\n";  
  // we use (bufferAmount + negativeBuffer) because we need the updated radius (10 + -3 = 7)  
  std::cout << "Expected:" << expectedArea((bufferAmount + negativeBuffer), static_cast<double>(pointsPerCircle)) << "\n";  
  
return 0;  
}  
```

---

## Comment 7

> Username: barendgehrels  
> Created at: 2024-11-25 18:34:21 UTC  
> Url: https://github.com/boostorg/geometry/issues/1284#issuecomment-2498760788  

`36000` is a bit absurd, but with `360` you get:  
```  
314.143  
314.143  
Detected:153.897  
Expected:153.93  
```
