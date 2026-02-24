# #657 - Incorrect results when transforming from 4326 to 3785/3857 [Closed]

> Username: kurtronshausen  
> Created at: 2020-01-10 20:00:06 UTC  
> Updated at: 2021-12-02 14:46:55 UTC  
> Closed at: 2020-04-17 04:46:25 UTC  
> Url: https://github.com/boostorg/geometry/issues/657  

The results look correct when I use a proj4 string to create my transformation, but if I use boost::geometry::srs::epsg it's about ~25km south of what I expect.   
  
```c++  
#include <boost/geometry.hpp>  
#include <boost/geometry/srs/epsg.hpp>  
#include <boost/geometry/srs/transformation.hpp>  
#include <iostream>  
  
int main() {  
  using point_geo = bg::model::point<double, 2, bg::cs::geographic<bg::degree>>;  
  using point_car = bg::model::point<double, 2, bg::cs::cartesian>;  
  
  point_geo original = point_geo(-114.7399212, 36.0160698);  
  point_car epsg_out;  
  point_car proj_out;  
  
  bg::srs::transformation<> epsg_tr{bg::srs::epsg(4326),  
                                                       bg::srs::epsg(3785)};  
  bg::srs::transformation<> proj_tr{  
      bg::srs::epsg(4326),  
      bg::srs::proj4(  
          "+proj=merc +a=6378137 +b=6378137 +lat_ts=0.0 +lon_0=0.0 +x_0=0.0 "  
          "+y_0=0 +k=1.0 +units=m +nadgrids=@null +wktext  +no_defs")};  
  
  epsg_tr.forward(original, epsg_out);  
  proj_tr.forward(original, proj_out);  
  std::cout << std::setprecision(12) << "epsg output: " << bg::get<0>(epsg_out) << " "  
            << bg::get<1>(epsg_out) << std::endl;  
  
  std::cout << std::setprecision(12) << "proj output: " << bg::get<0>(proj_out) << " "  
            << bg::get<1>(proj_out) << std::endl;  
  
  return 0;  
}  
```  
  
epsg output: -12772789.6016 4277696.96863  
proj output: -12772789.6016 4302832.77709

---

## Comment 1

> Username: awulkiew  
> Created at: 2020-02-04 16:43:23 UTC  
> Url: https://github.com/boostorg/geometry/issues/657#issuecomment-582001596  

Thanks. It seems that https://epsg.io/3785 and https://spatialreference.org/ref/epsg/popular-visualisation-crs-mercator/ list different proj4 string for the projection. The difference is that the latter defines towgs84 parameter and the former doesn't. So the latter takes the coordinates as if they were on the ellipsoid and the former converts between WGS84 ellipsoid and sphere before applying mercator projection.  
  
epsg.io defines `TOWGS84[0,0,0,0,0,0,0]` in WKT but not in proj4 string.  
  
It seems Proj took the string from epsg.io as well: https://github.com/OSGeo/PROJ/blob/5.2/nad/epsg#L4605  
  
What makes you think that one is correct and the other is not?

---

## Comment 2

> Username: mloskot  
> Created at: 2020-02-04 17:14:47 UTC  
> Url: https://github.com/boostorg/geometry/issues/657#issuecomment-582017263  

>  bg::srs::transformation<> epsg_tr{bg::srs::epsg(4326), bg::srs::epsg(3785)};  
  
I'd suggest to stop using the deprecated EPSG codes (`3785` or `3587`) for the popular visualisation spherical mercator as they are often *wrongly defined*. All sane software should stop support of those deprecated codes to stop the headache proliferation :-)  
  
The official code is `3857` http://www.epsg-registry.org/export.htm?gml=urn:ogc:def:crs:EPSG::3857

---

## Comment 3

> Username: awulkiew  
> Created at: 2020-02-08 13:36:16 UTC  
> Url: https://github.com/boostorg/geometry/issues/657#issuecomment-583737215  

@mloskot  I propose to use the `3857` definition for `3785` and `3587` since AFAIU this is what is expected from these SRIDs. Does that make sense?

---

## Comment 4

> Username: mloskot  
> Created at: 2020-02-08 20:32:35 UTC  
> Updated at: 2020-02-08 20:32:52 UTC  
> Url: https://github.com/boostorg/geometry/issues/657#issuecomment-583774079  

Yes, aliasing of those two deprecated codes to the official `3857` definition seems a good idea for user's convenience, especially definition-wise those are equivalents as https://wiki.openstreetmap.org/wiki/EPSG:3857 says:  
  
> EPSG:3785 - EPSG added this, similiar to how ESRI:102113 is defined (equivalent, deprecated)  
> EPSG:3587 - was never a real EPSG code, but some used this likely as a mistype of EPSG:3785 (typo)  
> EPSG:3857 - Equivalent to the depreciated code EPSG:3785.

---

## Comment 5

> Username: kurtronshausen  
> Created at: 2020-02-18 00:50:33 UTC  
> Url: https://github.com/boostorg/geometry/issues/657#issuecomment-587224208  

Thanks for the explanations.  I saw "equivalent" and expected it to behave exactly the same as 3857, which it's not.

---

## Comment 6

> Username: mloskot  
> Created at: 2020-02-27 11:31:39 UTC  
> Url: https://github.com/boostorg/geometry/issues/657#issuecomment-591923806  

@grokse What's the status of this?

---

## Comment 7

> Username: awulkiew  
> Created at: 2020-02-27 13:56:26 UTC  
> Url: https://github.com/boostorg/geometry/issues/657#issuecomment-591983485  

@mloskot It has to be fixed in these files before master branch is closed for 1.73:  
https://github.com/boostorg/geometry/blob/develop/include/boost/geometry/srs/projections/epsg.hpp  
https://github.com/boostorg/geometry/blob/develop/include/boost/geometry/srs/projections/epsg_traits.hpp

---

## Comment 8

> Username: mloskot  
> Created at: 2020-02-27 18:07:18 UTC  
> Url: https://github.com/boostorg/geometry/issues/657#issuecomment-592098437  

@awulkiew I was wrong in https://github.com/boostorg/geometry/issues/657#issuecomment-583774079 agreeing with you on https://github.com/boostorg/geometry/issues/657#issuecomment-583737215 :-)  
  
Here are some references:  
  
1. [[josm-dev] projection Mercator, set EPSG 3785 instead of EPSG 3857](https://lists.openstreetmap.org/pipermail/josm-dev/2011-March/005432.html)  
  
    > First of all, EPSG has totally purged EPSG:3785 from their database,  
    > at least as of version 7.6. The proj.4 epsg file [1] lists identical  
    > parameters for both EPSG:3785 and EPSG:3857, though comments that  
    > EPSG:3785 is deprecated. Basically EPSG:3785 should never have existed.  
  
2. https://alastaira.wordpress.com/2011/01/23/the-google-maps-bing-maps-spherical-mercator-projection/  
  
    > Also, despite the fact that EPSG registry normally retains details of deprecated codes,  
    > 3785 has mysteriously vanished from the database completely,   
  
  
    and  
  
    > The projection parameters defined above for EPSG:3785 describes a Mercator projection of  
    > geographic coordinates defined on a spherical model of the earth, of radius 6,378,137m,  
    > as specified on the following line:  
  
    > ```  
    > SPHEROID[“Popular Visualisation Sphere”, 6378137, 0, AUTHORITY[“EPSG”,”7059″]]  
    > ```  
  
    > The EPSG:3857 projection, in contrast, suggests a Mercator projection of coordinates  
    > defined on the WGS84 ellipsoid, with an inverse-flattening ratio of 298.257223563:  
  
    > ```  
    > SPHEROID[“WGS 84”, 6378137, 298.257223563, AUTHORITY[“EPSG”,”7030″]]  
    > ```  
  
    > The problem is that neither of these really describes the Google / Bing Maps projection,  
    > in which the underlying geographic coordinates are defined using WGS84 (as in 3857),  
    > but projected as if they were defined on a sphere (as in 3785).  
      
4. What PROJ 7.0.0 says:  
  
    ```  
    $ ./projinfo --main-db-path /mnt/d/proj.wsl/_build.gcc/data/proj.db EPSG:3587 | head -n 6  
    PROJ.4 string:  
    +proj=lcc +lat_0=43.3166666666667 +lon_0=-84.3666666666667 +lat_1=45.7 +lat_2=44.1833333333333 +x_0=6000000 +y_0=0 +ellps=GRS80 +towgs84=0,0,0,0,0,0,0 +units=m +no_defs +type=crs  
  
    WKT2:2019 string:  
    PROJCRS["NAD83(NSRS2007) / Michigan Central",  
        BASEGEOGCRS["NAD83(NSRS2007)",  
    ...  
    ```  
  
    ```  
    $ ./projinfo --main-db-path /mnt/d/proj.wsl/_build.gcc/data/proj.db EPSG:3785 | head -n 12  
    Warning: object is deprecated  
    Alternative non-deprecated CRS:  
    EPSG:3857  
  
    PROJ.4 string:  
    +proj=merc +a=6378137 +b=6378137 +lat_ts=0 +lon_0=0 +x_0=0 +y_0=0 +k=1 +units=m +nadgrids=@null +wktext +no_defs +type=crs  
  
    WKT2:2019 string:  
    PROJCRS["Popular Visualisation CRS / Mercator",  
        BASEGEOGCRS["Popular Visualisation CRS",  
            DATUM["Popular Visualisation Datum",  
                ELLIPSOID["Popular Visualisation Sphere",6378137,0,  
    ...  
    ```  
  
    ```  
    $ ./projinfo --main-db-path /mnt/d/proj.wsl/_build.gcc/data/proj.db EPSG:3857 | head -n 8  
    PROJ.4 string:  
    +proj=merc +a=6378137 +b=6378137 +lat_ts=0 +lon_0=0 +x_0=0 +y_0=0 +k=1 +units=m +nadgrids=@null +wktext +no_defs +type=crs  
  
    WKT2:2019 string:  
    PROJCRS["WGS 84 / Pseudo-Mercator",  
        BASEGEOGCRS["WGS 84",  
            DATUM["World Geodetic System 1984",  
                ELLIPSOID["WGS 84",6378137,298.257223563,  
    ...  
    ```  
  
------  
  
The `EPSG::3587` is ` NAD83(NSRS2007) / Michigan Central` so it should not be considered as alias.  
  
The `EPSG::3785` no longer exists in EPSG and BG has no marking of deprecation (i.e. as PROJ does),  
so purist approach should be to avoid it, remove it too.  
If we still want it in, then it should be defined as displayed by PROJ above.

---

## Comment 9

> Username: awulkiew  
> Created at: 2020-04-17 04:46:22 UTC  
> Url: https://github.com/boostorg/geometry/issues/657#issuecomment-615038894  

Thanks again for the report and discussion. The fix will be released with 1.73.

---

## Comment 10

> Username: audrew  
> Created at: 2021-12-02 14:46:55 UTC  
> Url: https://github.com/boostorg/geometry/issues/657#issuecomment-984694640  

why `["EPSG:3857","+proj=merc +a=6378137 +b=6378137 +lat_ts=0.0 +lon_0=0.0 +x_0=0.0 +y_0=0 +k=1.0 +units=m +nadgrids=@null +wktext  +no_defs"],`  doesn't transform correctly from `['EPSG:4326','+title=WGS 84 (long/lat) +proj=longlat +ellps=WGS84 +datum=WGS84 +units=degrees'],` ? Lat Lon to x y by meters?
