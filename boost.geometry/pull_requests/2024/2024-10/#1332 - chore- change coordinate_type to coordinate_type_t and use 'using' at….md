# #1332 chore: change coordinate_type to coordinate_type_t and use 'using' at… [Merged]

> Username: barendgehrels  
> Created at: 2024-10-28 21:30:37 UTC  
> Updated at: 2024-12-14 11:13:04 UTC  
> Merged at: 2024-10-30 13:24:54 UTC  
> Closed at: 2024-10-30 13:24:54 UTC  
> Url: https://github.com/boostorg/geometry/pull/1332  

… those places

---

## Review 1 [Commented]

> Username: barendgehrels  
> Created_at: 2024-10-28 21:31:38 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1332#pullrequestreview-2400280219  

📁 include/boost/geometry/algorithms/detail/extreme_points.hpp

```diff
 126 |-     typedef typename geometry::point_type<Ring>::type point_type;
 125 |+     using coordinate_type = geometry::coordinate_type_t<Ring>;
 126 |+     using point_type = geometry::point_type_t<Ring>;
```

> Username: barendgehrels  
> Created_at: 2024-10-28 21:31:38 UTC  
> Updated_at: 2024-10-28 21:31:50 UTC  
> Url: https://github.com/boostorg/geometry/pull/1332#discussion_r1819793359  

The ones for `point_type_t` preceded by `geometry::` were missed in last PR and included now


---

## Review 2 [Approved]

> Username: vissarion  
> Created_at: 2024-10-29 09:29:24 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/geometry/pull/1332#pullrequestreview-2401258721  

Thanks!

---
