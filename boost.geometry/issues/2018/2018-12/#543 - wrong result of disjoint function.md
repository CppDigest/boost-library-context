# #543 - wrong result of disjoint function [Closed]

> Username: tty2099  
> Created at: 2018-12-29 10:18:56 UTC  
> Updated at: 2022-11-22 15:02:31 UTC  
> Closed at: 2022-11-22 15:02:31 UTC  
> Url: https://github.com/boostorg/geometry/issues/543  

```cpp  
#include <boost/geometry/geometry.hpp>  
#include <boost/geometry/geometries/point_xy.hpp>  
#include <boost/geometry/geometries/polygon.hpp>  
#include <vector>  
#include <iostream>  
  
int main(void)  
{  
    using namespace boost::geometry;  
    typedef model::d2::point_xy<double> point_2d;  
    typedef model::polygon<point_2d> polygon_2d;  
    polygon_2d ply0, ply1;  
    std::vector<point_2d> v0 {point_2d(586,446), point_2d(308,280), point_2d(347,117), point_2d(716,438)};  
    std::vector<point_2d> v1 {point_2d(622,1027), point_2d(185,987), point_2d(175,980), point_2d(129,827), point_2d(238,786), point_2d(446,905)};  
    assign_points(ply0, v0);  
    assign_points(ply1, v1);  
    std::cout<<disjoint(ply0, ply1)<<std::endl;  
    return 0;  
}  
  
```  
  
On Ubuntu 16.04 with the default boost 1.58, the result is true, which is correct.  
  
But with the latest boost release 1.69, the result is false, which is a wrong answer. I checked it in win10 and archlinux

---

## Comment 1

> Username: vissarion  
> Created at: 2022-11-22 15:02:30 UTC  
> Url: https://github.com/boostorg/geometry/issues/543#issuecomment-1323816849  

The issue with the example above is that the created polygons are not valid (e.g. `is_valid(ply0)` returns `false`). Thus, the result is unexpected. If you correctly declare them (e.g. by applying `correct()` function to both polygons) then `disjoint` returns the correct result.
