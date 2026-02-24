# #1032 - Visual Studio warning C4457: declaration of 'median' hides function parameter in boost\geometry\index\detail\rtree\pack_create.hpp [Closed]

> Username: wanghan02  
> Created at: 2022-07-08 09:29:11 UTC  
> Updated at: 2022-07-11 18:29:51 UTC  
> Closed at: 2022-07-11 18:29:43 UTC  
> Url: https://github.com/boostorg/geometry/issues/1032  

Simple code as below using boost 1.79.0 triggers Visual Studio 19/22 warning C4457 with Warning LEVEL4 (`/W4`):   
  
`boost\geometry\index\detail\rtree\pack_create.hpp(88,49): warning C4457: declaration of 'median' hides function parameter`  
  
```  
#include <boost/geometry.hpp>  
#include <iostream>  
#include <tuple>  
  
int main()  
{  
    typedef boost::geometry::model::point<double, 2, boost::geometry::cs::cartesian> point_t;  
    typedef boost::geometry::model::polygon<point_t> polygon_t;  
  
    polygon_t polygon1{{{0.0, 0.0}, {0.0, 5.0}, {5.0, 5.0}}};  
  
    polygon_t polygon2{{{4.0, 4.0}, {1.0, 4.0}, {1.0, 1.0}}};  
  
    std::ignore = boost::geometry::distance(polygon1, polygon2);  
  
    return 0;  
}  
```

---

## Comment 1

> Username: awulkiew  
> Created at: 2022-07-11 18:29:51 UTC  
> Url: https://github.com/boostorg/geometry/issues/1032#issuecomment-1180731327  

Thanks.
