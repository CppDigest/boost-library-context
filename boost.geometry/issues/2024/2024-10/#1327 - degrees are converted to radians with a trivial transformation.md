# #1327 - degrees are converted to radians with a trivial transformation [Closed]

> Username: storm-ptr  
> Created at: 2024-10-10 08:44:29 UTC  
> Updated at: 2024-10-11 07:06:27 UTC  
> Closed at: 2024-10-10 08:57:11 UTC  
> Url: https://github.com/boostorg/geometry/issues/1327  

code  
```  
#include <boost/geometry.hpp>  
#include <boost/geometry/srs/epsg.hpp>  
#include <boost/geometry/srs/projection.hpp>  
#include <iostream>  
  
using point_t = boost::geometry::model::d2::  
    point_xy<double, boost::geometry::cs::geographic<boost::geometry::degree>>;  
  
using proj_t =  
    boost::geometry::srs::projection<boost::geometry::srs::static_epsg<4326>>;  
  
int main()  
{  
    auto pj = proj_t{};  
    auto a = point_t{-123., 20.};  
    auto b = point_t{};  
    pj.forward(a, b);  
    std::cout << "{.lon=" << b.x() << ", .lat=" << b.y() << "}\n";  
}  
```  
  
output  
```  
{.lon=-2.14675, .lat=0.349066}  
```  
  
[godbolt](https://godbolt.org/z/x9on1h9W8)

---

## Comment 1

> Username: vissarion  
> Created at: 2024-10-10 08:57:11 UTC  
> Url: https://github.com/boostorg/geometry/issues/1327#issuecomment-2404507178  

Is it a question? If you want to open a discussion or ask something "Discussions" maybe a better place to post such a topic.

---

## Comment 2

> Username: storm-ptr  
> Created at: 2024-10-11 07:06:26 UTC  
> Url: https://github.com/boostorg/geometry/issues/1327#issuecomment-2406688253  

https://github.com/boostorg/geometry/discussions/1328
