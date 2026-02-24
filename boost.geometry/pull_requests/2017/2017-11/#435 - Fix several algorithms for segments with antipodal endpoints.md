# #435 Fix several algorithms for segments with antipodal endpoints. [Merged]

> Username: awulkiew  
> Created at: 2017-11-20 20:46:51 UTC  
> Updated at: 2017-11-23 04:48:04 UTC  
> Merged at: 2017-11-22 17:43:20 UTC  
> Closed at: 2017-11-22 17:43:20 UTC  
> Url: https://github.com/boostorg/geometry/pull/435  

This PR fixes handling of geometries containing segments having antipodal endpoints in particular with both endpoints at equator. It affects andoyer inverse, thomas direct and sjoberg intersection formulas as well as envelope algorithm.  
  
Initially detected for `difference()` of these two geometries:  
  
    g1 = "LINESTRING(0 0, -3.14159265358979 0)"  
    g2 = "MULTILINESTRING((-2.1467549799530232 -0.12217304763960295,-2.5481807079117185 -0.90757121103705041,-2.6529004630313784 0.85521133347722067,0.92502450355699373 0.62831853071795796,-2.5307274153917754 0,2.8099800957108676 1.0646508437165401,-1.6057029118347816 -1.5009831567151219,0.2268928027592626 1.0646508437165401,-2.199114857512853 -0.017453292519943278,0 0.31415926535897898,0 0.57595865315812822,1.0471975511965967 -0.73303828583761765,2.1118483949131366 -0.54105206811824158))"  
  
which looks like this:  
  
![aaa](https://user-images.githubusercontent.com/1226951/33040647-fe1eeeb0-ce3b-11e7-88dd-12bb2323497b.png)

---

## Comment 1

> Username: vissarion  
> Created_at: 2017-11-22 11:09:24 UTC  
> Url: https://github.com/boostorg/geometry/pull/435#issuecomment-346319153  

Thanks Adam for the fix. I have a comment. Regarding the inverse formulas (e.g. andoyer) there is (still) an inconsistency: the returned azimuths will be w.r.t. the segment that follows the meridian but the distance will be w.r.t. the segment that follows the equator (which is longer). I know that this is a problem of all those formulas and it is fixed in distance strategy by calling the meridian formula but I think that we should at least put a comment for future reference.

---

## Comment 2

> Username: awulkiew  
> Created_at: 2017-11-22 13:03:53 UTC  
> Url: https://github.com/boostorg/geometry/pull/435#issuecomment-346344187  

Yes, I guess this should be a part of a "universal" (i.e. consistent across formulas) solution for antipodal regions. It's not clear to me if this should be done inside formulas (like this special case in andoyer or like differential_quantities are called inside, maybe that's wrong) or outside (like meridian distance). But ok I'll put a comment there about the inconsistency.  
  
I also plan to make it more predictable, i.e. currently the segment goes through the north pole if `lat1 <= lat2` and through south pole otherwise. I'll make it going through the north pole in all cases besides when lat1 is the north pole.

---
