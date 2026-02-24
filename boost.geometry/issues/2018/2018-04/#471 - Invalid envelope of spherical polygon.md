# #471 - Invalid envelope of spherical polygon [Closed]

> Username: awulkiew  
> Created at: 2018-04-09 17:25:48 UTC  
> Updated at: 2018-04-12 14:16:41 UTC  
> Closed at: 2018-04-12 14:16:41 UTC  
> Url: https://github.com/boostorg/geometry/issues/471  

It seems envelope() function return incorrect bounding box for spherical polygon in Boost 1.67.0.  
  
To reproduce:  
  
    #include <boost/geometry.hpp>  
    #include <iostream>  
  
    using coordinate_system = boost::geometry::cs::spherical_equatorial<boost::geometry::degree>;  
    using lonlat = boost::geometry::model::point<double, 2, coordinate_system>;  
    using box = boost::geometry::model::box<lonlat>;  
    using polygon = boost::geometry::model::polygon<lonlat>;  
    using linestring = boost::geometry::model::linestring<lonlat>;  
  
    int main()  
    {  
        polygon p{{{  
                {2.4, 48.9021},  
                {2.4, 48.89},  
                {2.3, 48.89},  
                {2.3, 48.9021},  
                {2.4, 48.9021}  
        }}};  
        linestring l{  
                {2.4, 48.9021},  
                {2.4, 48.89},  
                {2.3, 48.89},  
                {2.3, 48.9021},  
                {2.4, 48.9021}  
        };  
  
        box mbr1 = boost::geometry::return_envelope<box>(p);  
        box mbr2 = boost::geometry::return_envelope<box>(l);  
  
        std::cout << std::setprecision(7) << std::fixed;  
        std::cout << boost::geometry::wkt(mbr1.min_corner()) << " " << boost::geometry::wkt(mbr1.max_corner()) << std::endl;  
        std::cout << boost::geometry::wkt(mbr2.min_corner()) << " " << boost::geometry::wkt(mbr2.max_corner()) << std::endl;  
    }  
  
Result:  
  
    POINT(2.3000000 48.8900000) POINT(2.4000000 48.9021000)  
    POINT(2.3000000 48.8900000) POINT(2.4000000 48.9021108)  
  
The reason is that in the case of polygon envelope is calculated by iterating through points while in the case of linestring segments are taken into account.

---

## Comment 1

> Username: awulkiew  
> Created at: 2018-04-09 17:26:27 UTC  
> Url: https://github.com/boostorg/geometry/issues/471#issuecomment-379829789  

I'd expect the same issue with geographic CS.
