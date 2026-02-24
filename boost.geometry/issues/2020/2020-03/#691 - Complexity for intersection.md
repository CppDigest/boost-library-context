# #691 - Complexity for intersection [Closed]

> Username: rconde01  
> Created at: 2020-03-31 23:22:00 UTC  
> Updated at: 2020-04-19 03:12:41 UTC  
> Closed at: 2020-04-19 03:12:41 UTC  
> Url: https://github.com/boostorg/geometry/issues/691  

The complexity for intersection is not available in the documentation.  
  
https://www.boost.org/doc/libs/1_72_0/libs/geometry/doc/html/geometry/reference/algorithms/intersection/intersection_3.html  
  
Is it located somewhere else? Or is it known but not documented?

---

## Comment 1

> Username: awulkiew  
> Created at: 2020-04-09 14:37:04 UTC  
> Updated at: 2020-04-09 14:37:11 UTC  
> Url: https://github.com/boostorg/geometry/issues/691#issuecomment-611563103  

No it is not. Internally we use space partitioning similar to kd-tree and sorting intersection points by the position on geometry so the average complexity is something around linearitmic in terms of number of segments in geometries and number of intersections found. It of course depends on the data as in all cases when space partitioning is involved.
