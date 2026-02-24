# #427 - lat/lon lon/lat confusion [Closed]

> Username: aleksandrm8  
> Created at: 2017-10-11 14:51:43 UTC  
> Updated at: 2017-10-13 18:34:04 UTC  
> Closed at: 2017-10-11 15:28:13 UTC  
> Url: https://github.com/boostorg/geometry/issues/427  

In GIS systems we used to see latitude before longitude. But if I want to get distance between two points with boost::haversive, we should construct points from longitude and latitude (vise versa).   
IMHO order of parameters in function haversine::calculate should be changed.  
https://github.com/boostorg/geometry/blob/16402bcd83c7fbfd4361d2ba8b858ac48bfc59c7/include/boost/geometry/strategies/spherical/distance_haversine.hpp#L86

---

## Comment 1

> Username: barendgehrels  
> Created at: 2017-10-11 15:28:13 UTC  
> Url: https://github.com/boostorg/geometry/issues/427#issuecomment-335849605  

Thanks for your question.  
But no, this is an explicit choice, and it should not be changed. We use lola everywhere.  
Also in GIS the order is often used as LoLa so I'm sure you can get used to that.

---

## Comment 2

> Username: awulkiew  
> Created at: 2017-10-11 16:59:47 UTC  
> Updated at: 2017-10-11 17:00:28 UTC  
> Url: https://github.com/boostorg/geometry/issues/427#issuecomment-335877216  

FYI, this is only a problem if you use Boost.Geometry Point model like `bg::model::point<>`. You can also implement your own Point type taking latitude as the first constructor parameter and adapt it to Boost.Geometry Point concept. See e.g.:  
http://www.boost.org/doc/libs/1_65_1/libs/geometry/doc/html/geometry/reference/adapted/register/boost_geometry_register_point_2d.html  
  
Something like this should work:  
  
    struct lalo_point  
    {  
        lalo_point(double la, double lo) : lat(la), lon(lo) {}  
        double lat, lon;  
    };  
  
    BOOST_GEOMETRY_REGISTER_POINT_2D(lalo_point, double, cs::spherical_equatorial<degree>, lon, lat)  
  
    /*...*/  
  
    bg::distance(lalo_point(15, 95), lalo_point(16, 100));  
  
Or if you really want to use the distance strategy explicitly:  
  
    bg::strategy::distance::haversine<double>().apply(lalo_point(15, 95), lalo_point(16, 100));

---

## Comment 3

> Username: mloskot  
> Created at: 2017-10-11 19:25:14 UTC  
> Url: https://github.com/boostorg/geometry/issues/427#issuecomment-335920513  

I've added relevant paragraph to the Design Rationale page, see #428

---

## Comment 4

> Username: barendgehrels  
> Created at: 2017-10-13 18:34:04 UTC  
> Url: https://github.com/boostorg/geometry/issues/427#issuecomment-336533636  

@awulkiew : thanks for the great suggestion  
@mloskot : thanks for adding this to the documentation, super
