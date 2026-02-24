# #423 Fix L/A set operations [Merged]

> Username: awulkiew  
> Created at: 2017-09-12 14:56:04 UTC  
> Updated at: 2017-10-25 21:28:03 UTC  
> Merged at: 2017-10-25 21:28:03 UTC  
> Closed at: 2017-10-25 21:28:03 UTC  
> Url: https://github.com/boostorg/geometry/pull/423  

This PR contains two fixes for the following bugs:  
  
1. Regression in set operations caused by a different fix done in the past https://github.com/boostorg/geometry/pull/386 removing midpoint calculation. It manifests with 1.65 when both linestring's endpoints lie on a boundary but the interior of the linestring is in the exterior of polygon. In the following case the linestring is considered to be inside polygon so e.g. the result of difference(L, A, L) is empty:  
  
![obraz](https://user-images.githubusercontent.com/1226951/30331901-4205fb6c-97d9-11e7-9b96-86a9d60cc370.png)  
  
2. Lack of intersection point if the first endpoint of a linestring lies on a boundary of polygon. E.g. in the following case the result of intersection(L, A, P) is empty:  
  
![obraz](https://user-images.githubusercontent.com/1226951/30332413-8153a1a6-97da-11e7-8167-7536e186f046.png)  
  
Both bugs were fixed by using linear/areal turn operation, turn info policy and less function obejct for sorting turns (implemented for `relate`) in `intersection_insert` and slightly adapting the algorithms. In the case of L/A->L the old code was left for now in `intersection_insert` and `follow`. It can be enabled by defining `BOOST_GEOMETRY_SETOPS_LA_OLD_BEHAVIOR`.

---
