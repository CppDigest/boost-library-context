# #885 - Intersection of multi polygons gives wrong result [Closed]

> Username: linmma  
> Created at: 2021-07-06 14:55:31 UTC  
> Updated at: 2021-07-12 17:02:52 UTC  
> Closed at: 2021-07-09 06:31:20 UTC  
> Url: https://github.com/boostorg/geometry/issues/885  

Please have a look at this example:  
https://godbolt.org/z/xKKnY6xG4  
  
As you can see version 1.75 is used.  
  
This is the code:  
  
```cpp  
#include <boost/geometry.hpp>  
#include <iostream>  
  
namespace bg = boost::geometry;  
  
#define BOOST_GEOMETRY_NO_ROBUSTNESS  
  
int main() {  
  using Point = boost::geometry::model::d2::point_xy<double>;  
  using Polygon = boost::geometry::model::polygon<Point>;  
  using MultiPolygon = boost::geometry::model::multi_polygon<Polygon>;  
  
  MultiPolygon p{  
      {{  
          {3.4616329244067456e+03, -1.1312620150359478e+03},  
          {3.7189483279113028e+03, -9.7702588723416829e+02},  
          {2.1765870498935083e+03, 1.5961281478114047e+03},  
          {1.9192718066159621e+03, 1.4418921160504894e+03},  
          {3.4616337940386579e+03, -1.1312614937742705e+03},  
          {3.4616329244067456e+03, -1.1312620150359478e+03},  
      }},  
      {{  
          {-1.1654509096466320e+03, 6.5882000901007605e+03},  
          {-6.5133034962066631e+02, 5.7304821587511751e+03},  
          {8.9103079437709266e+02, 3.1573280433733316e+03},  
          {1.3593616776516128e+03, 2.3760020563583662e+03},  
          {-1.1654509096466320e+03, 6.5882000901007605e+03},  
      }},  
  };  
  
  MultiPolygon q{  
      {{  
          {-1.2682749948478361e+03, 6.7597436924371614e+03},  
          {-6.5133048364071806e+02, 5.7304820784189324e+03},  
          {-3.9401508013616058e+02, 5.8847182062207121e+03},  
          {-1.0109595913432786e+03, 6.9139798202389411e+03},  
          {-1.2682749948478361e+03, 6.7597436924371614e+03},  
      }},  
  };  
  
  bg::correct(p);  
  bg::correct(q);  
  
  bg::validity_failure_type failureP = bg::validity_failure_type::no_failure;  
  bg::validity_failure_type failureQ = bg::validity_failure_type::no_failure;  
  
  const auto isValidP = bg::is_valid(p, failureP);  
  const auto isValidQ = bg::is_valid(q, failureQ);  
  
  std::cout << "isValidP: " << std::boolalpha << isValidP << '\n';  
  std::cout << "isValidQ: " << std::boolalpha << isValidQ << '\n';  
  
  const auto areaP = bg::area(p);  
  const auto areaQ = bg::area(q);  
  
  std::cout << "areaP: " << areaP << '\n';  
  std::cout << "areaQ: " << areaQ << '\n';  
  
  if (isValidP && isValidQ) {  
    MultiPolygon r;  
  
    bg::intersection(p, q, r);  
  
    bg::validity_failure_type failureR = bg::validity_failure_type::no_failure;  
    const auto isValidR = bg::is_valid(r, failureR);  
  
    std::cout << "isValidR: " << std::boolalpha << isValidR << '\n';  
  
    const auto areaR = bg::area(r);  
  
    std::cout << "areaR: " << areaR << '\n';  
  
    std::cout << "r equals q: " << std::boolalpha << bg::equals(r,q) << '\n';  
  }  
}  
```  
  
The intersection of the two multi polygons p (red) and q (green)  
  
![polygons-p-q](https://user-images.githubusercontent.com/87026039/124620551-d58b0900-de79-11eb-9089-484c713f91ed.png)  
  
results in the wrong multi polygon r (blue)  
  
![polygons-p-q-intersection-r](https://user-images.githubusercontent.com/87026039/124620562-d885f980-de79-11eb-89c5-5a3ebed500fb.png)  
  
I tried it with and without BOOST_GEOMETRY_NO_ROBUSTNESS defined.

---

## Comment 1

> Username: kleunen  
> Created at: 2021-07-08 18:32:51 UTC  
> Url: https://github.com/boostorg/geometry/issues/885#issuecomment-876655897  

P has a self-intersection ?   
https://wandbox.org/permlink/FHSSdAxkRyorJdf6  
  
````  
isValidP: false Geometry has invalid self-intersections. A self-intersection point was found at (28.6703, 4596.02); method: i; operations: i/u; segment IDs {source, multi, ring, segment}: {0, 1, -1, 0}/{0, 1, -1, 2}  
isValidQ: true  
areaP: 899998  
areaQ: 360000

---

## Comment 2

> Username: linmma  
> Created at: 2021-07-09 06:31:20 UTC  
> Url: https://github.com/boostorg/geometry/issues/885#issuecomment-876950041  

Hi Kleunen,  
thanks for your quick reply.  
  
I see I had to place the _#define BOOST_GEOMETRY_NO_ROBUSTNESS_ right before the boost include. Sorry, my fault.  
  
But without the _#define BOOST_GEOMETRY_NO_ROBUSTNESS_ p is valid. So obviously it is advisable to use the _#define BOOST_GEOMETRY_NO_ROBUSTNESS_.

---

## Comment 3

> Username: barendgehrels  
> Created at: 2021-07-12 17:02:52 UTC  
> Url: https://github.com/boostorg/geometry/issues/885#issuecomment-878442214  

> So obviously it is advisable to use the _#define BOOST_GEOMETRY_NO_ROBUSTNESS_.  
  
It will be the default as soon as possible (but not yet in 1.78)
