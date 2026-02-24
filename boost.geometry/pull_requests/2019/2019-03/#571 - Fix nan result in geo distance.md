# #571 Fix nan result in geo distance [Merged]

> Username: vissarion  
> Created at: 2019-03-15 11:29:21 UTC  
> Updated at: 2019-03-15 12:49:44 UTC  
> Merged at: 2019-03-15 12:49:40 UTC  
> Closed at: 2019-03-15 12:49:40 UTC  
> Url: https://github.com/boostorg/geometry/pull/571  

caused by out of bounds argument in `acos` function

---

## Review 1 [Approved]

> Username: awulkiew  
> Created_at: 2019-03-15 12:24:21 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/geometry/pull/571#pullrequestreview-214991649  

📁 include/boost/geometry/strategies/geographic/distance_cross_track.hpp

```diff
 441 |+         {
 442 |+             s14 = acos(cos_frac) * earth_radius;
 443 |+         }
```

> Username: awulkiew  
> Created_at: 2019-03-15 11:48:51 UTC  
> Updated_at: 2019-03-15 12:46:27 UTC  
> Url: https://github.com/boostorg/geometry/pull/571#discussion_r265949076  

What do you think about rephrasing it like this:  
  
    CT s14 = cos_frac >= 1 ? CT(0)  
           : cos_frac <= -1 ? pi * earth_radius  
           : acos(cos_frac) * earth_radius;


---
