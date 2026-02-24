# #536 - base_tmerc_ellipsoid not initialized properly [Closed]

> Username: tpecholt  
> Created at: 2018-12-03 16:23:59 UTC  
> Updated at: 2018-12-10 15:38:19 UTC  
> Closed at: 2018-12-10 15:38:19 UTC  
> Url: https://github.com/boostorg/geometry/issues/536  



---

## Comment 1

> Username: tpecholt  
> Created at: 2018-12-03 16:28:53 UTC  
> Url: https://github.com/boostorg/geometry/issues/536#issuecomment-443773181  

* detail::tmerc::setup() doesn't initialize proj_param.en in case when par.es == 0. That leads to wrong computation with inf values.  
* can be fixed by moving pj_enfn() call to the beginning of setup() function  
* I am getting this behavior for `+proj=tmerc +lat_0=48.693665 +lon_0=9.002912 +ellps=sphere +k_0=1.0 +x_0=4567.9 +y_0=4488.4`

---

## Comment 2

> Username: awulkiew  
> Created at: 2018-12-03 19:33:18 UTC  
> Url: https://github.com/boostorg/geometry/issues/536#issuecomment-443840126  

Thanks for trying out the projections!  
  
It's correct that `en` member is not initialized. It's because spherical version of this projection should be used in this case, i.e.: `base_tmerc_spheroid` (the `spheroid` name is wrong btw, it's legacy name).  
  
How are you using this projection in your code?  
Are you creating an object of `tmerc_ellipsoid` by yourself?  
  
These classes are implementation details (even though they are in `boost::geometry::projections` namespace, it's legacy which should probably be changed). The intended usage at this moment looks like this:  
  
    bg::srs::projection<> prj = bg::srs::proj4("+proj=tmerc +lat_0=48.693665 +lon_0=9.002912 +ellps=sphere +k_0=1.0 +x_0=4567.9 +y_0=4488.4");  
  
    prj.forward(geometry_ll, geometry_xy);  
  
or  
  
    bg::srs::transformation<> tr(  
        bg::srs::proj4("+proj=longlat +ellps=sphere"),  
        bg::srs::proj4("+proj=tmerc +lat_0=48.693665 +lon_0=9.002912 +ellps=sphere +k_0=1.0 +x_0=4567.9 +y_0=4488.4")  
    );  
  
    tr.forward(geometry_ll, geometry_xy);  
  
where `geometry_ll` and `geometry_xy` can be any geometries supported by the library, e.g. polygons.  
  
There are also other possibilities, e.g. you can express parameters directly in C++ code both in run-time or in compile-time but for now leave it at that because you'd need develop branch or Boost 1.69 for this. More info: https://github.com/boostorg/geometry/pull/496.

---

## Comment 3

> Username: tpecholt  
> Created at: 2018-12-04 12:21:55 UTC  
> Url: https://github.com/boostorg/geometry/issues/536#issuecomment-444081498  

You are right I am using tmerc_ellipsoid directly. I don't have access to newest boost version as the linux machine is difficult to upgrade. So I currently have boost 1.64 and some older version of this repo and I just copied the content of geometry/extensions/ over. So there is no srs/ directory. The code looks like this:  
```c++  
auto params = boost::geometry::projections::init(proj4);  
boost::geometry::projections::tmerc_ellipsoid<point_ll, point_xy> projectionEllipsoid(params);  
projectionEllipsoid.forward(...)  
```  
For the new version will it work if I copy geometry/srs/ folder instead? Is it still compatible with boost 1.64? Or is there a way how to initialize general projection object such as srs::projection in older version of this lib?

---

## Comment 4

> Username: awulkiew  
> Created at: 2018-12-04 13:58:12 UTC  
> Updated at: 2018-12-04 14:03:29 UTC  
> Url: https://github.com/boostorg/geometry/issues/536#issuecomment-444108378  

If you want to use the internals directly (`tmerc_ellipsoid`/`tmerc_spheroid`) you have to make sure that you're using the correct class based on the ~*ellps* parameter~ `params.es == 0`.  
  
Otherwise you could use the factory which then was intended to be the automatic way of creating the correct projection from proj4 string, see:  
https://github.com/boostorg/geometry/blob/99be9e7afdda88cda92f37b910223436fbb60f71/extensions/test/gis/projections/projections.cpp#L64  
which is in the state corresponding to Boost 1.64.  
  
So in your case it'd look like this:  
  
    bg::projections::parameters params = bg::projections::detail::pj_init_plus("+proj=tmerc +lat_0=48.693665 +lon_0=9.002912 +ellps=sphere +k_0=1.0 +x_0=4567.9 +y_0=4488.4");  
    bg::projections::factory<point_geo_t, point_cart_t, bg::projections::parameters> pf;  
  
    boost::shared_ptr<bg::projections::projection<point_geo_t, point_cart_t> > prj(pf.create_new(params));  
  
    point_geo_t ll /* = ...*/;  
    point_cart_t xy /* = ...*/;  
    prj->forward(ll, xy);
