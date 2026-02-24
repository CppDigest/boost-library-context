# #466 - disjoint unexpectedly returns true [Closed]

> Username: csommelet  
> Created at: 2018-03-22 18:42:56 UTC  
> Updated at: 2019-03-27 16:59:42 UTC  
> Closed at: 2019-03-27 16:59:42 UTC  
> Url: https://github.com/boostorg/geometry/issues/466  

In some cases, disjoint function does not return the expected value. See code below for a failing example:  
  
```c++  
#include <boost/geometry.hpp>  
#include <iostream>  
  
using coordinate_system = boost::geometry::cs::spherical_equatorial<boost::geometry::degree>;  
using lonlat = boost::geometry::model::point<double, 2, coordinate_system>;  
using box = boost::geometry::model::box<lonlat>;  
using polygon = boost::geometry::model::polygon<lonlat>;  
  
int main()  
{  
    std::cout << "Boost version : " << BOOST_LIB_VERSION << " (" << BOOST_VERSION << ")" << std::endl;  
  
    const std::vector<double> norths{  
        48.9021,  
        48.90215,  
        48.9022,  
    };  
  
    for (const auto& north: norths) {  
        if (boost::geometry::disjoint(  
            box{  
                {2.2, 48.88},  
                {2.5, 48.9021}  
            },  
            polygon{{{  
                {2.4, north},  
                {2.4, 48.89},  
                {2.3, 48.89},  
                {2.3, north},  
            }}}))  
            std::cerr << "Unexpected disjoint for north lat : " << std::setprecision(7) << north << std::endl;  
    }  
  
    return 0;  
}  
```  
Output:  
```  
Boost version : 1_66 (106600)  
Unexpected disjoint for north lat : 48.90215  
```  
  
Reproduced when compiled with Apple Clang:  
```  
Apple LLVM version 9.0.0 (clang-900.0.39.2)  
Target: x86_64-apple-darwin17.4.0  
```  
  
Some investigation shows that this happens because the method general_areal::apply receives contradictory information:  
- ```disjoint_linear<Geometry1, Geometry2>::apply(geometry1, geometry2, strategy)``` returns true, because, schematically, it considers latitudes 48.90215 and 48.9021 to be close enough to be considered equal  
- ```rings_containing(geometry2, geometry1, strategy.template get_point_in_geometry_strategy<Geometry1, Geometry2>())``` returns false because it considers point with latitude 48.90215 to be outside the box

---

## Comment 1

> Username: awulkiew  
> Created at: 2018-04-09 17:30:24 UTC  
> Url: https://github.com/boostorg/geometry/issues/466#issuecomment-379831041  

Thanks! I can reproduce the problem. This is indeed a bug but I'd like to discourage you from checking relations of boxes and polygons or performing any other operation. At least in spherical or geographic.  
  
Some context: Internally when these geometries are mixed a box is treated like polygon, at least this is the intention. This is a legacy from a time when only cartesian was supported. In cartesian a box can be represented as polygon. However in spherical and geographic it cannot because in Boost.Geometry polygons have edges defined by geodesics (lines of shortest distance on the surface, e.g. great circles in spherical) but boxes have edges defined by meridians and parallels (which are not geodesics besides some exceptions). So boxes and polygons are not interchangeable. In fact this code probably shouldn't even compile but it is like it is.  
  
As a workaround you can replace box with polygon:  
  
    boost::geometry::disjoint(  
    polygon{{{  
                {2.2, 48.88},  
                {2.2, 48.9021},  
                {2.5, 48.9021},  
                {2.5, 48.88},  
                {2.2, 48.88} // your polygon type is closed!  
            }}},  
        polygon{{{  
                {2.4, north},  
                {2.4, 48.89},  
                {2.3, 48.89},  
                {2.3, north},  
                {2.4, north} // your polygon type is closed!  
            }}});  
  
This returns correct result for me. Of course assuming this is what you really want to do because as I said a box is not interchangeable with polygon in spherical. If possible you should not mix polygons with boxes. So either check relational predicates for polygons or calculate bounding boxes of these polygons and then before testing polygons test bounding boxes.  
  
Unfortunately it seems that currently (1.67.0) envelope returns wrong bounding box for polygons.  
See: https://github.com/boostorg/geometry/issues/471  
Not for linestrings it seems. So another workaround would be to convert exterior ring to linestring and calculate its bounding box.

---

## Comment 2

> Username: awulkiew  
> Created at: 2019-03-21 19:16:51 UTC  
> Url: https://github.com/boostorg/geometry/issues/466#issuecomment-475367186  

Polygon envelope issue mentioned above was fixed in 1.68  
for cross-reference: https://github.com/boostorg/geometry/issues/471  
fixed in: https://github.com/boostorg/geometry/pull/472  
later improved in: https://github.com/boostorg/geometry/pull/533

---

## Comment 3

> Username: awulkiew  
> Created at: 2019-03-27 16:59:23 UTC  
> Url: https://github.com/boostorg/geometry/issues/466#issuecomment-477254486  

Fixed in https://github.com/boostorg/geometry/pull/581
