# #1295 - boost::geometry::intersection, wrong result (result polygon is equal to the biggest of the input polygons) [Closed]

> Username: yaroslav-zlot  
> Created at: 2024-07-26 12:22:54 UTC  
> Updated at: 2024-11-15 17:35:22 UTC  
> Closed at: 2024-11-15 17:35:20 UTC  
> Url: https://github.com/boostorg/geometry/issues/1295  

For 1.85.0 with MSVC2022 the result of boost::geometry::intersection for certain input is wrong.  
Coordinate type: double  
  
when some of the points of different input polygons are close to each other and  
the values for the coordinates are very small (like 4.6030847803960675e-16)  
boost::geometry::intersection produces completely wrong result:  
  
In the test example one polygon is roughly contained in the other so the result of the intersection has to be   
about the same as the smaller polygon, but instead of it the result is the containing polygon.  
  
Setting the values to zero, for those small values, workarounds it.  
  
For 1.74.0 the result is expected.  
  
In the pictures, the first polygon is in red, the second is in green and the intersection is in black, thinner than for the input polygons.  
  
Wrong result for 1.85.0  
![1 85_wrong_result](https://github.com/user-attachments/assets/ce0ee4cf-4f8b-4334-863a-943d5b68fbc2)  
  
Expected result for 1.74.0 or 1.85.0 when small values are set to zero  
![1 74_or_1 85_with_nearly_zero_coordinates_set_to_zero](https://github.com/user-attachments/assets/3f4bdc34-3b9b-48d7-a3e7-6d9dd5a02346)  
  
```c++  
#include "boost/geometry/algorithms/intersection.hpp"  
#include "boost/geometry/algorithms/is_valid.hpp"  
#include "boost/geometry/geometries/polygon.hpp"  
#include "boost/geometry/geometries/multi_polygon.hpp"  
#include "boost/geometry/geometries/point_xy.hpp"  
  
#include <cassert>  
  
int main(int, char *[])  
{  
    using point_2d = boost::geometry::model::d2::point_xy<double>;  
    using BoostPolygon = boost::geometry::model::polygon<point_2d>;  
    using BoostMultiPolygon = boost::geometry::model::multi_polygon<BoostPolygon>;  
    using Ring = BoostPolygon::ring_type;  
  
    auto verifyIntersectionAreaIsLessOrEqualThanAreasOfInputPolygons = [](const auto& polygon1, const auto& polygon2)  
    {  
        assert(boost::geometry::is_valid(polygon1) && boost::geometry::is_valid(polygon2));  
        const auto areaOfPolygon1 = boost::geometry::area(polygon1);  
        const auto areaOfPolygon2 = boost::geometry::area(polygon2);  
        const double eps = 1e-7;  
        if (areaOfPolygon1 >= eps && areaOfPolygon2 >= eps)  
        {  
            BoostMultiPolygon intersection;  
            boost::geometry::intersection(polygon1, polygon2, intersection);  
            assert(boost::geometry::is_valid(intersection));  
            const auto areaOfIntersection = boost::geometry::area(intersection);  
            assert(areaOfIntersection < std::min(areaOfPolygon1, areaOfPolygon2) + eps);  
        }  
    };  
    {  
        // setting very small values to zero, begaviour is expected  
        BoostPolygon polygon1;  
        polygon1.outer() = Ring{{6.006524919981425, -1.8204168107748433},  
                                {-1.7115898840974395, -1.8204168107748442},  
                                {-2.1474675179419931, 1.8204168107748429},  
                                {-0.21793881692227707, 1.8204168107748435},  
                                {0.0, 0.0},  
                                {5.7885861030591492, 0.0},  
                                {6.006524919981425, -1.8204168107748433}};  
        BoostPolygon polygon2;  
        polygon2.outer() = Ring{{5.7885861030591501, 0.0},  
                                {4.9808473496052885, -0.91020845951432006},  
                                {0.0, -0.91020843804041685},  
                                {0.0, 0.0},  
                                {5.7885861030591501, 0.0}};  
        verifyIntersectionAreaIsLessOrEqualThanAreasOfInputPolygons(polygon1, polygon2);  
    }  
    {  
        // with very small values the result is completely wrong  
        BoostPolygon polygon1;  
        polygon1.outer() = Ring{{6.006524919981425, -1.8204168107748433},  
                                {-1.7115898840974395, -1.8204168107748442},  
                                {-2.1474675179419931, 1.8204168107748429},  
                                {-0.21793881692227707, 1.8204168107748435},  
                                {0.0, 0.0},  
                                {5.7885861030591492, 4.6030847803960675e-16},  
                                {6.006524919981425, -1.8204168107748433}};  
        BoostPolygon polygon2;  
        polygon2.outer() = Ring{{5.7885861030591501, 1.2258588412299519e-16},  
                                {4.9808473496052885, -0.91020845951432006},  
                                {3.0646473238074286e-16, -0.91020843804041685},  
                                {0.0, 0.0},  
                                {5.7885861030591501, 1.2258588412299519e-16}};  
        verifyIntersectionAreaIsLessOrEqualThanAreasOfInputPolygons(polygon1, polygon2);  
    }  
    return 0;  
}  
  
```

---

## Comment 1

> Username: vissarion  
> Created at: 2024-07-26 14:46:54 UTC  
> Url: https://github.com/boostorg/geometry/issues/1295#issuecomment-2252926974  

Thanks, I can reproduce it in Ubuntu 22.04 boost 1.85 with g++-9. As in similar cases it works with `long double`.   
Needs to be investigated more and fixed.

---

## Comment 2

> Username: barendgehrels  
> Created at: 2024-07-28 07:50:32 UTC  
> Url: https://github.com/boostorg/geometry/issues/1295#issuecomment-2254381501  

Indeed, I can reproduce it as well, on MacOS with clang.  
And, investigating #1294 yesterday, the concept fix I have there also works for this issue and it would be fixed by that.  
However, the concept fix is not yet 100% ready because it lets a few other cases failing, to be investigated.  
  
Concept fix: remove `cluster_exits` completely (it was designed as a simpler approach, to solve clusters turn problems, but apparently it has some problems and it is inconvenient to have two implementations for the same functionality). I hope to get it out.

---

## Comment 3

> Username: barendgehrels  
> Created at: 2024-07-29 07:41:29 UTC  
> Url: https://github.com/boostorg/geometry/issues/1295#issuecomment-2255224829  

The concept fix works and I hope to make a PR on Wednesday. It also fixes #1294   
@vissarion that might make it to 1.86 ?

---

## Comment 4

> Username: yaroslav-zlot  
> Created at: 2024-08-21 09:13:56 UTC  
> Url: https://github.com/boostorg/geometry/issues/1295#issuecomment-2301562064  

The original issue is fixed but there's still a problem:  
depending on the order of the input for boost::geometry::intersection the result might be wrong.  
  
Could you please check the following example:  
  
```c++  
#include "boost/geometry/algorithms/intersection.hpp"  
#include "boost/geometry/algorithms/is_valid.hpp"  
#include "boost/geometry/geometries/multi_polygon.hpp"  
#include "boost/geometry/geometries/point_xy.hpp"  
#include "boost/geometry/geometries/polygon.hpp"  
  
#include <cassert>  
  
int main(int, char *[])  
{  
    using point_2d = boost::geometry::model::d2::point_xy<double>;  
    using BoostPolygon = boost::geometry::model::polygon<point_2d>;  
    using BoostMultiPolygon = boost::geometry::model::multi_polygon<BoostPolygon>;  
    using Ring = BoostPolygon::ring_type;  
  
    auto verifyIntersectionAreaIsLessOrEqualThanAreasOfInputPolygons = [](const auto& polygon1, const auto& polygon2)  
    {  
        assert(boost::geometry::is_valid(polygon1) && boost::geometry::is_valid(polygon2));  
        const auto areaOfPolygon1 = boost::geometry::area(polygon1);  
        const auto areaOfPolygon2 = boost::geometry::area(polygon2);  
        const double eps = 1e-7;  
        if (areaOfPolygon1 >= eps && areaOfPolygon2 >= eps)  
        {  
            BoostMultiPolygon intersection;  
            boost::geometry::intersection(polygon1, polygon2, intersection);  
            assert(boost::geometry::is_valid(intersection));  
            const auto areaOfIntersection = boost::geometry::area(intersection);  
            assert(areaOfIntersection < std::min(areaOfPolygon1, areaOfPolygon2) + eps);  
        }  
    };  
    {  
        // setting very small values to zero, begavior is expected  
        Ring polygon1{{0.0, 0.0},  
                      {2.000000861538488, 2.0000008},  
                      {23.527865484476326, 2.0000008},  
                      {25.349718032203803, 1.569919497715026},  
                      {26.0, 0.0},  
                      {0.0, 0.0}};  
        Ring polygon2{{2.000000861538488,  0.0},  
                      {2.000000861538488,  2.0000008},  
                      {23.527865484476326,  2.0000008},  
                      {23.527865484476326,  0.0},  
                      {2.000000861538488,  0.0}};  
        verifyIntersectionAreaIsLessOrEqualThanAreasOfInputPolygons(polygon2, polygon1);  
        verifyIntersectionAreaIsLessOrEqualThanAreasOfInputPolygons(polygon1, polygon2);  
    }  
    {  
        // with very small values the result might be wrong depending  
        // on the order of the input passed to boost::geometry::intersection  
        Ring polygon1{{0.0, 2.9447116290680494e-16},  
                      {2.000000861538488, 2.0000008},  
                      {23.527865484476326, 2.0000008},  
                      {25.349718032203803, 1.569919497715026},  
                      {26.0, 2.7755575615628914e-15},  
                      {0.0, 2.9447116290680494e-16}};  
        Ring polygon2{{2.000000861538488,  2.9447116290680494e-16},  
                      {2.000000861538488,  2.0000008},  
                      {23.527865484476326,  2.0000008},  
                      {23.527865484476326,  2.9447116290680494e-16},  
                      {2.000000861538488,  2.9447116290680494e-16}};  
        // works  
        verifyIntersectionAreaIsLessOrEqualThanAreasOfInputPolygons(polygon2, polygon1);  
        // switching the order the result is incorrect  
        verifyIntersectionAreaIsLessOrEqualThanAreasOfInputPolygons(polygon1, polygon2);  
    }  
    return 0;  
}  
```

---

## Comment 5

> Username: barendgehrels  
> Created at: 2024-11-15 17:35:20 UTC  
> Url: https://github.com/boostorg/geometry/issues/1295#issuecomment-2479542891  

> The original issue is fixed but there's still a problem  
  
Please report new problem as a new issue. That makes the ticket management clearer.  
Closing this now, and copying your last code to a new issue.
