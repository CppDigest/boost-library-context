# #765 - Wrong result in geographic side strategy and side_by_cross_track spherical strategy [Closed]

> Username: vissarion  
> Created at: 2020-10-13 07:56:14 UTC  
> Updated at: 2020-12-21 10:33:22 UTC  
> Closed at: 2020-12-21 10:33:22 UTC  
> Url: https://github.com/boostorg/geometry/issues/765  

The geographic side strategy (here: https://github.com/boostorg/geometry/blob/develop/include/boost/geometry/strategies/geographic/side.hpp) returns wrong results for some cases when a point clearly lies on a geodesic defined by two other points. In particular, when two points coincide. The following example illustrates the issue.   
  
```  
#include <iostream>  
#include <boost/geometry.hpp>  
  
namespace bg = boost::geometry;  
  
int main()  
{  
    typedef bg::model::point<double, 2, bg::cs::geographic<bg::radian> > point;  
  
    point p1 {0,0};  
    point p2 {1,2};  
  
    using geo = bg::strategy::side::geographic<>;  
  
    std::cout << geo().apply(p1, p1, p2) << std::endl;  
    std::cout << geo().apply(p1, p2, p1) << std::endl;  
    std::cout << geo().apply(p2, p1, p1) << std::endl;  
  
    using sph = bg::strategy::side::spherical_side_formula<>;  
  
    std::cout << sph().apply(p1, p1, p2) << std::endl;  
    std::cout << sph().apply(p1, p2, p1) << std::endl;  
    std::cout << sph().apply(p2, p1, p1) << std::endl;  
  
    return 0;  
}  
```  
  
The result is   
```  
-1  
1  
0  
0  
0  
0  
```  
  
while it should be `0` everywhere. Note that the respective spherical strategy `strategy::side::spherical_side_formula` returns correct results.   
  
Compiled with `gcc version 9.3.0 (Ubuntu 9.3.0-11ubuntu0~18.04.1)` using the `develop` branch.

---

## Comment 1

> Username: raghavendrashekhawat2  
> Created at: 2020-10-14 09:32:23 UTC  
> Url: https://github.com/boostorg/geometry/issues/765#issuecomment-708282638  

Hi. i would like to work on this issue. Could i be assigned to it ?.

---

## Comment 2

> Username: vissarion  
> Created at: 2020-10-14 09:47:35 UTC  
> Url: https://github.com/boostorg/geometry/issues/765#issuecomment-708290892  

Hi, this is fixed here https://github.com/boostorg/geometry/pull/756

---

## Comment 3

> Username: vissarion  
> Created at: 2020-10-16 08:04:59 UTC  
> Url: https://github.com/boostorg/geometry/issues/765#issuecomment-709893880  

I similar issue exist for the spherical side strategy `side_by_cross_track` e.g. the following code  
```  
int main()  
{  
    typedef bg::model::point<double, 2, bg::cs::geographic<bg::degree> > point;  
  
    point p1 {0,0};  
    point p {360,0};  
    point p2 {1,2};  
  
    using sbct = bg::strategy::side::side_by_cross_track<>;  
  
    std::cout << sbct().apply(p1, p1, p2) << std::endl;  
    std::cout << sbct().apply(p1, p2, p1) << std::endl;  
    std::cout << sbct().apply(p2, p1, p1) << std::endl;  
  
    using ssf = bg::strategy::side::spherical_side_formula<>;  
  
    std::cout << ssf().apply(p1, p1, p2) << std::endl;  
    std::cout << ssf().apply(p1, p2, p1) << std::endl;  
    std::cout << ssf().apply(p2, p1, p1) << std::endl;  
  
    return 0;  
}  
```  
  
returns  
  
```  
-1  
1  
0  
0  
0  
0  
```
