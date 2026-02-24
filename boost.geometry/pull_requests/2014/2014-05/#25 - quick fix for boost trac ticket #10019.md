# #25 [overlay] quick fix for boost trac ticket #10019 [Merged]

> Username: mkaravel  
> Created at: 2014-05-09 06:37:43 UTC  
> Updated at: 2014-05-11 21:13:04 UTC  
> Merged at: 2014-05-11 21:13:04 UTC  
> Closed at: 2014-05-11 21:13:04 UTC  
> Url: https://github.com/boostorg/geometry/pull/25  

the specialization of intersection_insert for linestring/box computes the intersection, but pretends as if it works for difference as well;  
  
quick fix: further specialize this specialization of intersection insert so that it works only for overlay_intersection;  
problem and quick fix related to boost trac ticket #10019;

---
