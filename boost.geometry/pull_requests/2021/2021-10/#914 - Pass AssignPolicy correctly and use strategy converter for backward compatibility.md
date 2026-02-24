# #914 [self_turns] Pass AssignPolicy correctly and use strategy converter for backward compatibility. [Merged]

> Username: awulkiew  
> Created at: 2021-10-03 18:40:44 UTC  
> Updated at: 2021-10-17 14:26:13 UTC  
> Merged at: 2021-10-17 14:26:13 UTC  
> Closed at: 2021-10-17 14:26:13 UTC  
> Url: https://github.com/boostorg/geometry/pull/914  

This is a fix for: https://github.com/boostorg/geometry/issues/870  
  
Furthermore now `AssignPolicy` is passed instead of `assign_null` be used in all cases in overload taking `Reverse` parameter.

---

## Review 1 [Commented]

> Username: awulkiew  
> Created_at: 2021-10-12 19:05:01 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/914#pullrequestreview-777753036  

📁 include/boost/geometry/algorithms/detail/overlay/self_turn_points.hpp

```diff
 379 |     concepts::check<Geometry const>();
 380 | 
 300 |-     typedef detail::overlay::get_turn_info<detail::overlay::assign_null_policy> turn_policy;
```

> Username: awulkiew  
> Created_at: 2021-10-12 19:05:00 UTC  
> Updated_at: 2021-10-12 19:05:01 UTC  
> Url: https://github.com/boostorg/geometry/pull/914#discussion_r727414772  

Here `detail::overlay::assign_null_policy` was passed instead of `AssignPolicy`.


---
