# #496 Add run-time and compile-time projections/transformations parameters. [Merged]

> Username: awulkiew  
> Created at: 2018-06-28 02:35:18 UTC  
> Updated at: 2018-09-14 21:37:43 UTC  
> Merged at: 2018-09-14 21:37:43 UTC  
> Closed at: 2018-09-14 21:37:43 UTC  
> Url: https://github.com/boostorg/geometry/pull/496  

With this PR transformations' parameters can be expressed in following ways:  
  
- run-time Proj4 string  
  
      srs::proj4("+proj=tmerc +ellps=WGS84 +units=m");  
  
 - run-time  
  
       using namespace srs::dpar;  
       parameters<>(proj_tmerc)(ellps_wgs84)(units_m)  
  
- compile-time  
      
      using namespace srs::spar;  
      parameters<proj_tmerc, ellps_wgs84, units_m>  
  
This PR also changes the way of processing parameters in general. In the original code some parameters like `datum` or `ellps` were expanded which resulted in addition of new parameters into parameter list. Default parameters could also be added. I removed this mechanism because otherwise I'd be forced to implement rewriting of static parameters in compile-time which would increase the compilation time. the old behavior is emulated though. Now instead of expanding parameters and adding defaults at the beginning, the parameter is searched on the parameters list and if it's not found only then it may be extracted from other parameter (e.g. ellipsoid from datum) and after that the default may be used. A side effect of this change is that the initialization with Proj4 string is 2x faster in my test which is creation of transformation:  
  
    from = "+proj=longlat +ellps=WGS84 +datum=WGS84 +no_defs"  
    to = "+proj=aea +lat_1=34 +lat_2=40.5 +lat_0=0 +lon_0=-120 +x_0=0 +y_0=-4000000 +ellps=clrk66 +units=m +no_defs"  
  
Replacing string-based parameters with run-time parameters speeds up the creation another 5x. So WRT the previous implementation (string-based always modifying parameters by expanding them and adding defaults) the speedup is 10x.  
  
Replacing run-time parameters with compile-time parameters speeds up the creation another 2x, so WRT the original 20x.  
  
Furthermore it's still possible to initialize transformations passing SRID codes (EPSG, ESRI, IAU2000), but now newly introduced, non-string-based parameters are used for this which means the initialization is faster. Before Proj4 strings were used for this.  
  
I also improved const-corectness of the code, e.g. the input coordinates of projections are not modified so projections does not rely on the internal caller to work properly. This was affecting combined projections (projection calling another projection).

---

## Comment 1

> Username: mloskot  
> Created_at: 2018-06-28 06:30:02 UTC  
> Url: https://github.com/boostorg/geometry/pull/496#issuecomment-400926815  

This is awesome, thanks Adam for this overhaul!

---

## Comment 2

> Username: mloskot  
> Created_at: 2018-06-28 07:31:29 UTC  
> Url: https://github.com/boostorg/geometry/pull/496#issuecomment-400940219  

@awulkiew BTW, how is the PROJ.4 to Boost.Geometry conversion managed now? I can't find anything about the procedure. It used to be semi-automatic, AFAIR.

---

## Comment 3

> Username: awulkiew  
> Created_at: 2018-06-28 12:49:01 UTC  
> Url: https://github.com/boostorg/geometry/pull/496#issuecomment-401022789  

@mloskot Now it's fully manual. I guess it could be partially automated but it'd rely on consistency of Proj4 and the automation code would have to be re-written because both Proj4 and Boost.Geometry was changed since it was last used. I don't consider it worth it because it was used only for projections (code in `srs/projections/proj` directory) and they are easy to update manually. It also didn't analyse the code (it'd be a lot more complex if it did that), it generated artifacts which had to be removed by hand, the names were not changed (we still have upper-case names in the code), the indentation was wrong and still is in some cases, etc. The style of the original code was there and to be honest I don't like it: confusing way of writing the expressions, e.g. variables assigned internally in expressions, variables assigned in `if` conditions, reusing variables to temporarily hold some value not-related with the name of variable, using input parameters of functions to hold temporary values, declarations of variables at the beginning of the scope, use of pointers, etc.

---

## Comment 4

> Username: mloskot  
> Created_at: 2018-07-10 11:30:02 UTC  
> Url: https://github.com/boostorg/geometry/pull/496#issuecomment-403790641  

@awulkiew Your explanation makes sense. Thanks. It just made me wonder about the maintenance; how the updates in PROJ.4 are going to be captured, if at all; how to document the procedure, etc.

---

## Comment 5

> Username: barendgehrels  
> Created_at: 2018-07-31 16:00:37 UTC  
> Url: https://github.com/boostorg/geometry/pull/496#issuecomment-409274212  

Cool! Will look at it tomorrow

---

## Comment 6

> Username: barendgehrels  
> Created_at: 2018-08-05 20:06:21 UTC  
> Url: https://github.com/boostorg/geometry/pull/496#issuecomment-410544404  

I'm OK with merging this! Thanks a lot @awulkiew !

---

## Comment 7

> Username: vissarion  
> Created_at: 2018-08-21 08:42:55 UTC  
> Url: https://github.com/boostorg/geometry/pull/496#issuecomment-414598363  

Thanks, I am ok with merging.

---

## Comment 8

> Username: awulkiew  
> Created_at: 2018-09-14 21:37:32 UTC  
> Url: https://github.com/boostorg/geometry/pull/496#issuecomment-421491037  

Thanks for the reviews!

---
