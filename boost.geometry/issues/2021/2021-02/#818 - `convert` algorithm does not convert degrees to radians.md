# #818 - `convert` algorithm does not convert degrees to radians [Closed]

> Username: vissarion  
> Created at: 2021-02-23 11:13:19 UTC  
> Updated at: 2021-06-03 14:30:04 UTC  
> Closed at: 2021-02-24 16:13:11 UTC  
> Url: https://github.com/boostorg/geometry/issues/818  

I do not know whether this is a feature or not but `convert` algorithm does not convert degrees to radians and vice versa for geographic inputs (see example below).   
  
This could potentially confuse users that expect `convert` to convert also degrees to radians as in https://stackoverflow.com/questions/65493910/boost-point-circle-coming-out-in-weird-shapes/   
  
```  
#include <iostream>  
#include <boost/geometry.hpp>  
  
namespace bg = boost::geometry;  
  
int main()  
{  
    using dpoint = bg::model::point<double, 2, bg::cs::geographic<bg::degree>>;  
    using rpoint = bg::model::point<double, 2, bg::cs::geographic<bg::radian>>;  
  
    dpoint dp(10, 40);  
    rpoint rp;  
  
    bg::convert(dp, rp);  
  
    std::cout << "d --> r" << std::endl  
              << bg::get<0>(rp) << "," << bg::get<1>(rp) << std::endl  
              << bg::get_as_radian<0>(rp) << ","  
              << bg::get_as_radian<1>(rp) << std::endl  
              << bg::get<0>(dp) << "," << bg::get<1>(dp) << std::endl  
              << bg::get_as_radian<0>(dp) << ","  
              << bg::get_as_radian<1>(dp) << std::endl;  
  
    return 0;  
}  
```  
returns  
```  
d --> r  
10,40  
10,40  
10,40  
0.174533,0.698132  
```

---

## Comment 1

> Username: awulkiew  
> Created at: 2021-02-24 13:52:29 UTC  
> Url: https://github.com/boostorg/geometry/issues/818#issuecomment-785090121  

Something tells me that this is a feature. AFAIR `transform` can be used to convert between units.  
  
Furthermore this could be a breaking change.

---

## Comment 2

> Username: vissarion  
> Created at: 2021-02-24 16:13:11 UTC  
> Url: https://github.com/boostorg/geometry/issues/818#issuecomment-785189047  

Indeed, `trasform` converts between units.
