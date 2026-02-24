# #1449 - Boost.Geometry intersection() returns empty result in 1.84/85/86 but not in 1.72 for valid overlapping polygons [Open]

> Username: SowmyaKoppuravuri  
> Created at: 2026-02-19 07:02:36 UTC  
> Updated at: 2026-02-19 07:05:23 UTC  
> Url: https://github.com/boostorg/geometry/issues/1449  

We are observing a behavioral difference in boost::geometry::intersection() between Boost 1.72 and Boost 1.86.  
  
For the same valid input polygons:  
- bg::is_valid() returns true for both polygons  
- bg::intersects() returns true  
- Boost ≤ 1.72 returns a non-empty intersection polygon  
- Boost ≥ 1.84 (tested with 1.86) returns an empty result  
We are trying to determine whether this is:  
- an intended robustness change in overlay behavior,  
- degenerate result filtering, or a regression.  
  
**Environment**  
Working version:  
Boost 1.72  
MSVC 19.xx  
C++17  
  
Failing version:  
Boost 1.84/85/86  
MSVC 19.xx  
C++17  
  
**Minimal Reproducible Example**  
  
```  
#include <boost/geometry.hpp>  
#include <vector>  
#include <iostream>  
  
namespace bg = boost::geometry;  
  
using Point = bg::model::d2::point_xy<double>;  
using Polygon = bg::model::polygon<Point>;  
  
int main()  
{  
    Polygon a;  
    bg::append(a, Point(-2.7262688429326545, 16.826043180279360));  
    bg::append(a, Point(-2.7262688429326545, 17.976043180279358));  
    bg::append(a, Point(0.59999999999990905, 17.976043180279358));  
    bg::append(a, Point(0.59999999999990905, 16.826043180279360));  
    bg::append(a, Point(-2.7262688429326545, 16.826043180279360));  
  
    Polygon b;  
    bg::append(b, Point(2.5905051825378118, 20.985713718593683));  
    bg::append(b, Point(2.5905051825378118, 19.985713718593683));  
    bg::append(b, Point(0.60479136394412836, 18.000000000000000));  
    bg::append(b, Point(0.59999999999999998, 17.976043180279358));  
    bg::append(b, Point(0.57000000000000006, 17.826043180279360));  
    bg::append(b, Point(-2.6962688429326507, 17.826043180279360));  
    bg::append(b, Point(-2.7262688429326509, 17.976043180279358));  
    bg::append(b, Point(-2.7310602068767795, 18.000000000000000));  
    bg::append(b, Point(-4.6257992677318862, 19.894738960855108));  
    bg::append(b, Point(-4.6257992677318862, 20.985713718593683));  
    bg::append(b, Point(2.5905051825378118, 20.985713718593683));  
  
    bg::correct(a);  
    bg::correct(b);  
  
    std::cout << "is_valid(a): " << bg::is_valid(a) << "\n";  
    std::cout << "is_valid(b): " << bg::is_valid(b) << "\n";  
    std::cout << "intersects: " << bg::intersects(a, b) << "\n";  
  
    std::vector<Polygon> result;  
    bg::intersection(a, b, result);  
  
    std::cout << "intersection count: " << result.size() << "\n";  
  
    if (!result.empty())  
        std::cout << "area: " << bg::area(result[0]) << "\n";  
  
    return 0;  
}  
  
```  
  
**Observed Behavior :**  
**Boost 1.72:**  
intersects: 1  
intersection count: 1  
area: (non-zero)  
  
**Boost 1.86:**  
intersects: 1  
intersection count: 0  
  
**Question**  
  
Is this behavior difference expected due to overlay robustness/degenerate filtering changes introduced in later Boost versions, or is this considered a regression? The polygons are valid and clearly overlap geometrically.
