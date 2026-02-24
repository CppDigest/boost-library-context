# #989 - interesection between segment and ring not defined [Open]

> Username: MelisWillem  
> Created at: 2022-04-06 12:26:10 UTC  
> Updated at: 2022-11-29 15:26:35 UTC  
> Url: https://github.com/boostorg/geometry/issues/989  

The intersection operation is not defined between a segment and a ring. It is however defined between a linestring and a ring. Is this a feature or bug? What am I missing here?  
  
```  
#include <boost/geometry.hpp>  
  
namespace bg = boost::geometry;  
  
int main()  
{  
    using point = bg::model::point<double, 2, bg::cs::cartesian>;  
    using segment = bg::model::segment<point>;  
    using ring = bg::model::ring<point>;  
  
    ring r = {{0,0},{0,1},{1,1}, {1,0},{0,0}};  
    segment s = {{-1,-1}, {2,2}};  
  
    std::vector<point> points;  
    bg::intersection(s,r,points);  
  
    return 0;  
}  
```  
results in  
```  
error: static_assert failed due to requirement 'boost::geometry::detail::static_assert_check<false, boost::geometry::segment_tag, boost::geometry::model::segment<boost::geometry::model::point<double, 2, boost::geometry::cs::cartesian>>>::value' "Not or not yet implemented for this Geometry type."  
```  
  
try it on godbolt: https://godbolt.org/z/7sPnhcKWj

---

## Comment 1

> Username: vissarion  
> Created at: 2022-11-22 14:23:10 UTC  
> Url: https://github.com/boostorg/geometry/issues/989#issuecomment-1323756517  

I can reproduce it. This is a missing combination, with a simple workaround though.

---

## Comment 2

> Username: MelisWillem  
> Created at: 2022-11-29 15:26:33 UTC  
> Url: https://github.com/boostorg/geometry/issues/989#issuecomment-1330824173  

@vissarion yes I made a helper function in the codebase I was working on to implement this. I just wasn't sure if there was a specific reason you guy's didn't add this in the library itself.
