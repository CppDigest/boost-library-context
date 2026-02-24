# #880 - self-intersection aware simplify [Open]

> Username: kleunen  
> Created at: 2021-07-03 17:52:29 UTC  
> Updated at: 2023-06-20 07:53:41 UTC  
> Url: https://github.com/boostorg/geometry/issues/880  

In case of a polygon and multi-polygon, the current implementation of simplify may generate self-intersecting polygons/multi-polygons. For the tilemaker project I have implemented a self-intersection aware simplify:  
  
https://github.com/kleunen/boost_geometry_simplify  
  
It makes sure that:   
* The simplified inner and outer rings do not contain self-intersection  
* The outer does not overlap the inners  
* In case one inner overlaps another inner, it is combined into a single inner using union  
* In case one polygon in a multipolygon overlaps another polygon, it is combined into a single polygon using a union  
  
The simplification of the ring is performed using this algorithm:  
https://www.jasondavies.com/simplify/  
  
I believe this would be benificial for the boost geometry project and other projects as well.   
  
Have a try here: https://godbolt.org/z/rhhxjenYP

---

## Comment 1

> Username: barendgehrels  
> Created at: 2021-07-14 12:48:58 UTC  
> Url: https://github.com/boostorg/geometry/issues/880#issuecomment-879862470  

Sorry for my late reaction, I noticed this effort already earlier. It sounds awesome! I will have a close look later.

---

## Comment 2

> Username: kleunen  
> Created at: 2021-07-14 13:00:04 UTC  
> Url: https://github.com/boostorg/geometry/issues/880#issuecomment-879870082  

Yes, the github repository already has some performance enhancements and bugfixes. So it is best to checkout the example from the github repository.
