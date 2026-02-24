# #1196 - Incorrect results for overlaps (and possibly other functions) for spherical polygons with an edge on a pole [Open]

> Username: benjaminulmer  
> Created at: 2023-09-06 16:26:52 UTC  
> Updated at: 2023-12-06 10:08:43 UTC  
> Url: https://github.com/boostorg/geometry/issues/1196  

We've noticed overlaps is returning the incorrect answer when one of our spherical polygons has an edge that lies on one of the poles. Minimum reproducing example:  
![boost-bug](https://github.com/boostorg/geometry/assets/14302434/f7f0b229-4c6a-4517-b2b3-aec5fa5576c8)  
![boost-bug2](https://github.com/boostorg/geometry/assets/14302434/1fd12ccf-4cdc-48f1-b06e-8c3953666a8a)  
```c++  
#include <iostream>  
  
#include <boost/geometry.hpp>  
#include <boost/geometry/core/cs.hpp>  
#include <boost/geometry/geometries/point_xy.hpp>  
  
using LngLat = boost::geometry::model::d2::  
    point_xy<double, boost::geometry::cs::spherical_equatorial<boost::geometry::degree>>;  
  
template<class P>  
using PolygonT      = boost::geometry::model::polygon<P, false, false>;  
using LngLatPolygon = PolygonT<LngLat>;  
  
auto main() -> int {  
    LngLatPolygon llp1{{  
        {8.92160517405147324, 56.340695599039897},  
        {41.9960406999057341, 47.7074241201727958},  
        {49.1284599721614725, 69.2412195838126507},  
    }};  
  
    LngLatPolygon llp2{{  
        {-161.946888344631759, 62.1833404333998487},  
        {-155.724837495461969, 46.3736980797803398},  
        {-129.631351537537682, 55.6010415941354097},  
    }};  
  
    LngLatPolygon bigTriangle{{  
        {31.7174744114610121, 58.282525588538995},  
        {-148.282525588539016, 58.282525588538995},  
        {-58.282525588538995, 31.717474411461005},  
    }};  
  
    // All of these should be true  
    std::cout << boost::geometry::overlaps(llp1, bigTriangle) << std::endl;  
    std::cout << boost::geometry::overlaps(bigTriangle, llp1) << std::endl;  
  
    std::cout << boost::geometry::intersects(llp1, bigTriangle) << std::endl;  
    std::cout << boost::geometry::intersects(bigTriangle, llp1) << std::endl;  
  
    std::cout << boost::geometry::overlaps(llp2, bigTriangle) << std::endl;  
    std::cout << boost::geometry::overlaps(bigTriangle, llp2) << std::endl;  
  
    std::cout << boost::geometry::intersects(llp2, bigTriangle) << std::endl;  
    std::cout << boost::geometry::intersects(bigTriangle, llp2) << std::endl;  
  
    return 0;  
}  
  
```  
And the output I get when using Boost 1.83  
```  
0  
0  
1  
1  
0  
0  
1  
1  
```  
  
Based on my understanding of overlaps it should produce the same output as intersects for these cases. A colleague has also reported strange behaviour from `intersection` when dealing with this "bigTriangle", which I could try and look into as well if that's useful---I wouldn't be surprised if other functions have issues as well and would be happy to test them if requested.  
  
For reference, "bigTriangle" is one face of an icosahedron. A similar issues happens with one of the faces at the south pole, but interestingly, the mirror of "bigTriangle" that also has an edge on the pole doesn't exhibit these issues.   
  
I believe the edge crossing the pole is the issue because if you slightly change "bigTriangle" (for example, making the first vertex = `{31.71747441146, 58.282525588538995}`) it produces the expected output.

---

## Comment 1

> Username: benjaminulmer  
> Created at: 2023-09-06 17:23:31 UTC  
> Updated at: 2023-09-06 19:09:13 UTC  
> Url: https://github.com/boostorg/geometry/issues/1196#issuecomment-1708801994  

A colleague suggested we change the longitude domain from [-pi, pi] to [0, 2pi]. ~Doing this seems to fix the issue (at least in this example). Is this the expected domain for `spherical_equatorial`?~  
  
Doing this changed which triangle exhibits the problem; the issue now occurs with the face of the icosahedron sharing the pole edge with the one in the example. I'll get an updated minimum reproducing example soon.

---

## Comment 2

> Username: benjaminulmer  
> Created at: 2023-09-06 19:17:59 UTC  
> Url: https://github.com/boostorg/geometry/issues/1196#issuecomment-1708951987  

```c++  
#include <iostream>  
#include <numbers>  
  
#include <boost/geometry.hpp>  
#include <boost/geometry/core/cs.hpp>  
#include <boost/geometry/geometries/point_xy.hpp>  
  
using LngLat = boost::geometry::model::d2::  
    point_xy<double, boost::geometry::cs::spherical_equatorial<boost::geometry::degree>>;  
  
template<class P>  
using PolygonT      = boost::geometry::model::polygon<P, false, false>;  
using LngLatPolygon = PolygonT<LngLat>;  
  
auto main() -> int {  
    LngLatPolygon llp1{{  
        {8.92160517405147324, 56.340695599039897},  
        {41.9960406999057341, 47.7074241201727958},  
        {49.1284599721614725, 69.2412195838126507},  
    }};  
  
    LngLatPolygon llp2{{  
        {-161.946888344631759 + 360, 62.1833404333998487},  
        {-155.724837495461969 + 360, 46.3736980797803398},  
        {-129.631351537537682 + 360, 55.6010415941354097},  
    }};  
  
    LngLatPolygon bigTriangle{{  
        {31.7174744114610121, 58.282525588538995},  
        {121.717474411461012, 31.717474411461005},  
        {-148.282525588539016 + 360, 58.282525588538995},  
    }};  
  
    // All of these should be true  
    std::cout << boost::geometry::overlaps(llp1, bigTriangle) << std::endl;  
    std::cout << boost::geometry::overlaps(bigTriangle, llp1) << std::endl;  
  
    std::cout << boost::geometry::intersects(llp1, bigTriangle) << std::endl;  
    std::cout << boost::geometry::intersects(bigTriangle, llp1) << std::endl;  
  
    std::cout << boost::geometry::overlaps(llp2, bigTriangle) << std::endl;  
    std::cout << boost::geometry::overlaps(bigTriangle, llp2) << std::endl;  
  
    std::cout << boost::geometry::intersects(llp2, bigTriangle) << std::endl;  
    std::cout << boost::geometry::intersects(bigTriangle, llp2) << std::endl;  
  
    return 0;  
}  
```

---

## Comment 3

> Username: vissarion  
> Created at: 2023-12-06 10:08:38 UTC  
> Url: https://github.com/boostorg/geometry/issues/1196#issuecomment-1842569945  

Thanks for opening this issue. I can reproduce it. It is a bug.
