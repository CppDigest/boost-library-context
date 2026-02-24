# #580 Fix disjoint(segment, box) in spherical_equatorial and geographic CS [Merged]

> Username: vissarion  
> Created at: 2019-03-26 15:43:36 UTC  
> Updated at: 2019-03-27 08:01:22 UTC  
> Merged at: 2019-03-27 08:01:14 UTC  
> Closed at: 2019-03-27 08:01:14 UTC  
> Url: https://github.com/boostorg/geometry/pull/580  

Propose a fix for https://github.com/boostorg/geometry/issues/579

---

## Review 1 [Approved]

> Username: awulkiew  
> Created_at: 2019-03-26 16:54:08 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/geometry/pull/580#pullrequestreview-219028864  

📁 include/boost/geometry/algorithms/detail/disjoint/segment_box.hpp

```diff
 154 | 
 155 |         //Compute alp1 outside envelope and pass it to envelope_segment_impl
 156 |         //in order for it to be used later in the algorithm
```

> Username: awulkiew  
> Created_at: 2019-03-26 16:53:55 UTC  
> Updated_at: 2019-03-27 08:00:30 UTC  
> Url: https://github.com/boostorg/geometry/pull/580#discussion_r269210884  

This comment is not needed anymore.


---
