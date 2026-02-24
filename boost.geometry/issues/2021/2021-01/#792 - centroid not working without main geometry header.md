# #792 - centroid not working without main geometry header [Closed]

> Username: MelisWillem  
> Created at: 2021-01-05 08:15:45 UTC  
> Updated at: 2022-10-19 13:08:17 UTC  
> Closed at: 2022-10-19 13:08:17 UTC  
> Url: https://github.com/boostorg/geometry/issues/792  

When I try to use the centroid or return_centroid function, by including the bare minimum (#include <boost/geometry/algorithms/centroid.hpp>) it doesn't seem to compile.("error: no type named 'state_type'")  
  
If I add the "#include<boost/geometry.hpp>" header things work just fine. Is this by design? or a bug?  
  
example code:  
```  
// #include<boost/geometry.hpp> // uncomment me to get stuff compiling.  
#include <boost/geometry/algorithms/centroid.hpp>  
#include <boost/geometry/geometries/point_xy.hpp>  
#include <boost/geometry/geometries/linestring.hpp>  
  
namespace bg = boost::geometry;  
  
using point = bg::model::point<int, 2, bg::cs::cartesian>;  
using linestring = bg::model::linestring<point>;  
  
int main(){  
    auto l = linestring{{0,0},{1,1}};  
    auto p = bg::return_centroid<point>(l);  
}  
```  
Or on compiler explorer: https://godbolt.org/z/Wdcb6f

---

## Comment 1

> Username: vissarion  
> Created at: 2021-01-11 10:40:46 UTC  
> Url: https://github.com/boostorg/geometry/issues/792#issuecomment-757865956  

You need to include the stategies for centroid.   
Including the following should work  
```#include <boost/geometry/strategies/strategies.hpp>```  
However, AFAIU, this should not be needed to use the centroid algorithm.

---

## Comment 2

> Username: MelisWillem  
> Created at: 2021-01-11 11:04:40 UTC  
> Url: https://github.com/boostorg/geometry/issues/792#issuecomment-757878262  

@vissarion thanks !. With the "strategies" include it works, but the doc's don't say anything about it. I'l go trough the code and figure out why exactly the strategies header is required here.

---

## Comment 3

> Username: vissarion  
> Created at: 2022-10-19 13:08:17 UTC  
> Url: https://github.com/boostorg/geometry/issues/792#issuecomment-1283988038  

This is fixed in boost `1.77.0`.
