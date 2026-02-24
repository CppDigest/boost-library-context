# #1283 Fix covered_by incorrect results  [Merged]

> Username: vissarion  
> Created at: 2024-05-16 09:36:58 UTC  
> Updated at: 2024-07-24 13:44:45 UTC  
> Merged at: 2024-07-24 13:44:42 UTC  
> Closed at: 2024-07-24 13:44:42 UTC  
> Url: https://github.com/boostorg/geometry/pull/1283  

Fix covered_by incorrect results for polygons with multiple defined points as a pole.  
  
Resolves https://github.com/boostorg/geometry/issues/1279 (the special case arise as part of issue https://github.com/boostorg/geometry/issues/1161)

---

## Review 1 [Commented]

> Username: barendgehrels  
> Created_at: 2024-05-21 17:47:21 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1283#pullrequestreview-2069253419  

📁 include/boost/geometry/strategies/spherical/point_in_poly_winding.hpp

```diff
 399 |+         calc_t s2 = get<0>(seg2);
 400 |+ 
 401 |+         // In case of a segment that contains a pole endpoint we need an arbitrary but consistant
```

> Username: barendgehrels  
> Created_at: 2024-05-21 17:47:21 UTC  
> Updated_at: 2024-05-21 17:47:22 UTC  
> Url: https://github.com/boostorg/geometry/pull/1283#discussion_r1608714327  

-> `consistent`


---

## Review 2 [Approved]

> Username: barendgehrels  
> Created_at: 2024-05-21 17:47:36 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/geometry/pull/1283#pullrequestreview-2069253828  

👍

---
