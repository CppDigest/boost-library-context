# #397 Add missing relational operations for MultiPoint/Geometry [Merged]

> Username: awulkiew  
> Created at: 2017-05-04 13:53:10 UTC  
> Updated at: 2018-01-23 00:15:54 UTC  
> Merged at: 2017-05-23 11:24:20 UTC  
> Closed at: 2017-05-23 11:24:20 UTC  
> Url: https://github.com/boostorg/geometry/pull/397  

Added in `covered_by`, `crosses`, `disjoint`, `equals`, `relate`, `relation`, `intersects`, `touches`, `within`.  
  
Also divide the rest of relational operations into interface and implementation parts.

---

## Review 1 [Commented]

> Username: vissarion  
> Created_at: 2017-05-11 09:44:32 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/397#pullrequestreview-37528789  

📁 include/boost/geometry/algorithms/detail/equals/implementation.hpp

```diff
 229 |+ // is implemented in a way consistent with the Intersection/Side Strategy
 230 |+ // then collect_vectors is used, otherwise relate is used.
 231 |+ // NOTE: the result could be coneptually different for invalid
```

> Username: vissarion  
> Created_at: 2017-05-11 09:44:32 UTC  
> Updated_at: 2017-05-11 23:04:27 UTC  
> Url: https://github.com/boostorg/geometry/pull/397#discussion_r115947332  

*conceptually


---

## Review 2 [Commented]

> Username: vissarion  
> Created_at: 2017-05-11 09:51:02 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/397#pullrequestreview-37530238  

I am ok with merging.

---
