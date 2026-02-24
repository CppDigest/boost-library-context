# #1183 - union_ returns incomplete result polygon [Closed]

> Username: AndreMeyerRMC  
> Created at: 2023-08-14 11:12:25 UTC  
> Updated at: 2023-09-13 18:48:59 UTC  
> Closed at: 2023-09-13 18:48:59 UTC  
> Url: https://github.com/boostorg/geometry/issues/1183  

union_ returns an incomplete result polygon in a special case where large portions of the two input polygons overlap  
  
Tested with VS2017 (msvc-14.1), boost Versions 1.79.0, 1.82.0, 1.83.0  
  
![boost_union](https://github.com/boostorg/geometry/assets/86659038/481aebb6-002c-4f84-ba2e-ee09f98ea6c1)  
  
The upper small branch of the polygon is comletely lost in the result. The two shapes have a few points in common but do not overlap exactly in the vicinity of the lost branch.  
  
```  
using boost_coordinate_t = double;  
using boost_point_2d = boost::geometry::model::d2::point_xy<boost_coordinate_t>;  
using boost_polygon_2d = boost::geometry::model::polygon<boost_point_2d>;  
using boost_multipolygon_2d = boost::geometry::model::multi_polygon<boost_polygon_2d>;  
  
auto polyA = boost::geometry::from_wkt<boost_multipolygon_2d>("MULTIPOLYGON(((\  
-38841.000561763794 6721272.4648206839,\  
-38869.843144000479 6721327.7877584957,\  
-38880.685990792437 6721344.0451435195,\  
-38902.349206128216 6721371.1493254723,\  
-38937.993505971914 6721407.9151819283,\  
-38925.264019448201 6721389.3887558663,\  
-38925.240186032526 6721389.3524003429,\  
-38903.891993208345 6721355.1907963008,\  
-38909.181455691403 6721352.2237809654,\  
-38926.953387540903 6721388.3157829558,\  
-38940.906008282145 6721408.6223519640,\  
-38961.796165266409 6721432.5740491701,\  
-38961.819650679929 6721432.6019898951,\  
-38977.627018370375 6721452.1204930553,\  
-38977.724235665897 6721452.2645238554,\  
-38997.157657657241 6721487.2743892670,\  
-38997.156729985494 6721487.2749042027,\  
-38997.205159411656 6721487.3621566473,\  
-38995.456469315024 6721488.3327662209,\  
-38976.018586129794 6721453.3126770724,\  
-38959.181300235970 6721432.6429256191,\  
-38900.879954713615 6721372.5071140006,\  
-38900.816775977277 6721372.4353842726,\  
-38879.096795985606 6721345.2601805655,\  
-38879.046005852288 6721345.1907021403,\  
-38868.149375046065 6721328.8526752470,\  
-38868.094590395332 6721328.7601068122,\  
-38839.231040705585 6721273.3969513429,\  
-38841.000561763794 6721272.4648206839,\  
)))");  
auto polyB = boost::geometry::from_wkt<boost_multipolygon_2d>("MULTIPOLYGON(((\  
-38869.815523415571 6721327.7463451568,\  
-38880.685990792437 6721344.0451435195,\  
-38902.349206128216 6721371.1493254723,\  
-38960.647313876834 6721431.2817974398,\  
-38960.704662809898 6721431.3463021647,\  
-38977.625225408119 6721452.1182855805,\  
-38977.724248525541 6721452.2645470239,\  
-38997.205159411656 6721487.3621566473,\  
-38995.456469315024 6721488.3327662209,\  
-38976.018586130762 6721453.3126770733,\  
-38959.181300235970 6721432.6429256191,\  
-38900.879954713615 6721372.5071140006,\  
-38879.046005852288 6721345.1907021403,\  
-38868.149375046065 6721328.8526752470,\  
-38831.186802632437 6721272.4401722597,\  
-38832.882157764841 6721271.3783569681,\  
-38869.815523415571 6721327.7463451568\  
)))");  
  
    boost_multipolygon_2d result;  
    boost::geometry::union_(polyA, polyB, result);  
```

---

## Comment 1

> Username: AndreMeyerRMC  
> Created at: 2023-08-14 11:21:54 UTC  
> Url: https://github.com/boostorg/geometry/issues/1183#issuecomment-1677140717  

It seems to work very well when all points are rounded to the nearest  .1, but I'd rather not do that for each polygon because that may lead to self-intersecting (multi-)polygons.  
All input polygons are created by (multiple) boost::geometry::buffer - operation on single linestrings and union_ of the results, so they all should be valid.  
  
Thank you for maintaining a great library!

---

## Comment 2

> Username: barendgehrels  
> Created at: 2023-08-14 16:14:57 UTC  
> Url: https://github.com/boostorg/geometry/issues/1183#issuecomment-1677636936  

Thanks for yoru report and compliment.  
I cannot look at it right now, but will do later.  
To be sure, you test with the default setup? (no extra define for boost Geometry?)

---

## Comment 3

> Username: AndreMeyerRMC  
> Created at: 2023-08-15 06:32:08 UTC  
> Updated at: 2023-08-15 06:51:56 UTC  
> Url: https://github.com/boostorg/geometry/issues/1183#issuecomment-1678484922  

Just tested these shapes again in godbolt with clang 16 and boost 1.82.0 to make sure no weird settings are present.  
num_points returns: A: 29 B: 17 Result: 23  
Result should be more complex than A, so I'm pretty confident that error occurred even without proper visualization.  
  
```  
#include <iostream>  
  
#include <boost/geometry.hpp>  
  
using boost_coordinate_t = double;  
using boost_point_2d = boost::geometry::model::d2::point_xy<boost_coordinate_t>;  
using boost_polygon_2d = boost::geometry::model::polygon<boost_point_2d>;  
using boost_multipolygon_2d = boost::geometry::model::multi_polygon<boost_polygon_2d>;  
  
int main()  
{  
    auto polyA = boost::geometry::from_wkt<boost_multipolygon_2d>("MULTIPOLYGON(((\  
-38841.000561763794 6721272.4648206839,\  
-38869.843144000479 6721327.7877584957,\  
-38880.685990792437 6721344.0451435195,\  
-38902.349206128216 6721371.1493254723,\  
-38937.993505971914 6721407.9151819283,\  
-38925.264019448201 6721389.3887558663,\  
-38925.240186032526 6721389.3524003429,\  
-38903.891993208345 6721355.1907963008,\  
-38909.181455691403 6721352.2237809654,\  
-38926.953387540903 6721388.3157829558,\  
-38940.906008282145 6721408.6223519640,\  
-38961.796165266409 6721432.5740491701,\  
-38961.819650679929 6721432.6019898951,\  
-38977.627018370375 6721452.1204930553,\  
-38977.724235665897 6721452.2645238554,\  
-38997.157657657241 6721487.2743892670,\  
-38997.156729985494 6721487.2749042027,\  
-38997.205159411656 6721487.3621566473,\  
-38995.456469315024 6721488.3327662209,\  
-38976.018586129794 6721453.3126770724,\  
-38959.181300235970 6721432.6429256191,\  
-38900.879954713615 6721372.5071140006,\  
-38900.816775977277 6721372.4353842726,\  
-38879.096795985606 6721345.2601805655,\  
-38879.046005852288 6721345.1907021403,\  
-38868.149375046065 6721328.8526752470,\  
-38868.094590395332 6721328.7601068122,\  
-38839.231040705585 6721273.3969513429,\  
-38841.000561763794 6721272.4648206839,\  
)))");  
    auto polyB = boost::geometry::from_wkt<boost_multipolygon_2d>("MULTIPOLYGON(((\  
-38869.815523415571 6721327.7463451568,\  
-38880.685990792437 6721344.0451435195,\  
-38902.349206128216 6721371.1493254723,\  
-38960.647313876834 6721431.2817974398,\  
-38960.704662809898 6721431.3463021647,\  
-38977.625225408119 6721452.1182855805,\  
-38977.724248525541 6721452.2645470239,\  
-38997.205159411656 6721487.3621566473,\  
-38995.456469315024 6721488.3327662209,\  
-38976.018586130762 6721453.3126770733,\  
-38959.181300235970 6721432.6429256191,\  
-38900.879954713615 6721372.5071140006,\  
-38879.046005852288 6721345.1907021403,\  
-38868.149375046065 6721328.8526752470,\  
-38831.186802632437 6721272.4401722597,\  
-38832.882157764841 6721271.3783569681,\  
-38869.815523415571 6721327.7463451568\  
)))");  
  
    boost_multipolygon_2d result;  
    boost::geometry::union_(polyA, polyB, result);  
  
    std::cout << "A: " << boost::geometry::num_points(polyA)  
     << "\nB: " << boost::geometry::num_points(polyB)  
     << "\nR: " << boost::geometry::num_points(result);  
  
    return 0;  
}  
```

---

## Comment 4

> Username: barendgehrels  
> Created at: 2023-08-23 14:30:42 UTC  
> Url: https://github.com/boostorg/geometry/issues/1183#issuecomment-1690073492  

It's not the same issue as #1186   
Using `long double`, the result is fine for me.  
Using `double`, I can reproduce it.  
To be continued.

---

## Comment 5

> Username: barendgehrels  
> Created at: 2023-08-30 15:56:33 UTC  
> Url: https://github.com/boostorg/geometry/issues/1183#issuecomment-1699442288  

It's fixed in PR #1193  
I expect it can be merged next week (but it's holiday season, reviews are not quick now)
