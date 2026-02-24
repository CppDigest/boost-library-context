# #1211 - boost::geometry::intersection wrong result [Open]

> Username: mjagodicblackshark  
> Created at: 2023-10-25 13:24:44 UTC  
> Updated at: 2024-11-24 10:03:50 UTC  
> Url: https://github.com/boostorg/geometry/issues/1211  

On version 1.82 without special defines. I have found a test case that gives unexpectedly wrong results.  
  
Here is the test case to prove the problem. The correct result is close to the float precision limit, but the actual result is almost the same as one of the input polygons and therefore very wrong. Empty or even degenerate results would be fine for our use case. Possibly related to #1201   
  
```C++  
using Point = boost::geometry::model::d2::point_xy<float>;  
using BoostPolygonType = boost::geometry::model::polygon<Point, false, false>;  
using BoostMultiPolygonType = boost::geometry::model::multi_polygon<BoostPolygonType>;  
BoostPolygonType polygon1;  
boost::geometry::read_wkt("POLYGON((0.875 0.4375, 0.875 0.375, 0.9375 0.375))", polygon1);  
BoostPolygonType polygon2;  
boost::geometry::read_wkt("POLYGON((1. 0., 1. 0.581662834, 0.824054539 0.))", polygon2);  
  
BoostMultiPolygonType result;  
boost::geometry::intersection(polygon1, polygon2, result);  
  
EXPECT_EQ(result.size(), 1);  
  
EXPECT_LT(boost::geometry::area(result), 0.01);  
  
for (auto& intersection : result)  
{  
    EXPECT_EQ(intersection.outer().size(), 3);  
}  
```  
  
polygon1-orange, polygon2-red  
![image](https://github.com/boostorg/geometry/assets/92168821/394ee974-fe9d-4182-b704-4aab158d96c4)  
the result of the intersection  
![image](https://github.com/boostorg/geometry/assets/92168821/fd690dba-f5f1-4668-9767-08adad0bbe2e)

---

## Comment 1

> Username: jdmarsh  
> Created at: 2023-10-25 15:07:53 UTC  
> Url: https://github.com/boostorg/geometry/issues/1211#issuecomment-1779494620  

I've had this issue crop up as well (1.82 and 1.83). It's not quite the same but I've added a repro of my problem here:  
https://godbolt.org/z/W1s7YEczP  
with the points plotted here:   
https://www.desmos.com/calculator/zrytxj5d8u  
The output line should be very small as it only intersects at a corner, but the output seems to be much much larger going outside of the bounds of the polygon.

---

## Comment 2

> Username: vissarion  
> Created at: 2023-12-05 10:38:14 UTC  
> Url: https://github.com/boostorg/geometry/issues/1211#issuecomment-1840483390  

I can reproduce the issue. Probably a numerical issue. With `double` instead of `float` the results are as expected.

---

## Comment 3

> Username: jdmarsh  
> Created at: 2023-12-05 10:47:04 UTC  
> Url: https://github.com/boostorg/geometry/issues/1211#issuecomment-1840497182  

I've had this issue using float and double. I only managed to solve it by moving to cpp_bin_float_quad in boost multiprecision but that's crazy slow and I can't guarantee that the issue isn't still going to crop up there as well, I just haven't seen it yet.

---

## Comment 4

> Username: vissarion  
> Created at: 2023-12-05 13:22:36 UTC  
> Url: https://github.com/boostorg/geometry/issues/1211#issuecomment-1840784346  

@jdmarsh could you please open a new issue with your example and data?
