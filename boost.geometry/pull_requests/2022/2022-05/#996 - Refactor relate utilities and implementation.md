# #996 [relate] Refactor relate utilities and implementation. [Merged]

> Username: awulkiew  
> Created at: 2022-05-03 12:22:45 UTC  
> Updated at: 2022-05-29 19:32:38 UTC  
> Merged at: 2022-05-19 11:35:27 UTC  
> Closed at: 2022-05-19 11:35:27 UTC  
> Url: https://github.com/boostorg/geometry/pull/996  

Implement boundary_checker and follow helpers for geometry collection.  
They are commented-out for now because they are not used anywhere.  
The code is there in case they were needed in the future.

---

## Review 1 [Approved]

> Username: barendgehrels  
> Created_at: 2022-05-11 08:19:28 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/geometry/pull/996#pullrequestreview-968861565  

Thanks! I didn't check all details, but in general it looks simpler and better to me.

📁 include/boost/geometry/algorithms/detail/relate/areal_areal.hpp

```diff
 660 |-             typename detail::sub_range_return_type<Geometry const>::type
 661 |-                 range_ref = detail::sub_range(geometry, seg_id);
 650 |+             auto const& sub_range = detail::sub_range(geometry, seg_id);
```

> Username: barendgehrels  
> Created_at: 2022-05-11 08:08:32 UTC  
> Updated_at: 2022-05-11 08:19:28 UTC  
> Url: https://github.com/boostorg/geometry/pull/996#discussion_r869998470  

This should probably be kept as a reference. The subrange returns usually a reference, or something else (it can decide) but I think it should not be a reference here.

> Username: awulkiew  
> Created_at: 2022-05-12 13:18:17 UTC  
> Url: https://github.com/boostorg/geometry/pull/996#discussion_r871370017  

What do you mean by "kept as a reference"? That I should leave it as it was with type specified explicitly?  
  
The lifetime of a temporary object returned by`detail::sub_range` will be the same as `sub_range` const reference. So it doesn't matter if a reference or a value is returned.

> Username: barendgehrels  
> Created_at: 2022-05-29 19:32:37 UTC  
> Updated_at: 2022-05-29 19:32:38 UTC  
> Url: https://github.com/boostorg/geometry/pull/996#discussion_r884314893  

I know. But I find that C++ behavior weird.  
Anyway, I'll try to adapt.


📁 include/boost/geometry/algorithms/detail/relate/boundary_checker.hpp

```diff
  16 | #include <boost/geometry/algorithms/detail/equals/point_point.hpp>
  17 | #include <boost/geometry/algorithms/detail/sub_range.hpp>
  18 |+ //#include <boost/geometry/algorithms/detail/visit.hpp>
```

> Username: barendgehrels  
> Created_at: 2022-05-11 08:11:03 UTC  
> Updated_at: 2022-05-11 08:19:28 UTC  
> Url: https://github.com/boostorg/geometry/pull/996#discussion_r870001079  

Remove again?

> Username: awulkiew  
> Created_at: 2022-05-18 21:34:32 UTC  
> Updated_at: 2022-05-18 21:34:33 UTC  
> Url: https://github.com/boostorg/geometry/pull/996#discussion_r876401776  

Removed

---

📁 include/boost/geometry/algorithms/detail/relate/boundary_checker.hpp

```diff
  84 |+ 
  85 |+ template <typename Point, typename Strategy, typename Out>
  86 |+ inline void copy_pts_boundary_points(Point const& front_pt, Point const& back_pt,
```

> Username: barendgehrels  
> Created_at: 2022-05-11 08:12:17 UTC  
> Updated_at: 2022-05-11 08:19:28 UTC  
> Url: https://github.com/boostorg/geometry/pull/996#discussion_r870002242  

What is `pts`?

> Username: barendgehrels  
> Created_at: 2022-05-11 08:20:27 UTC  
> Url: https://github.com/boostorg/geometry/pull/996#discussion_r870010151  

> What is `pts`?  
  
I later saw you use `seg` and `ls` etc, so that part is consistent. But `pts` followed by `points` looks a bit weird.  
Anyway, you can ignore this comment if you want, it's a detail.

> Username: awulkiew  
> Created_at: 2022-05-12 13:40:26 UTC  
> Url: https://github.com/boostorg/geometry/pull/996#discussion_r871395828  

I agree that it looks wierd. I'll think about a replacement.

> Username: awulkiew  
> Created_at: 2022-05-18 21:35:33 UTC  
> Url: https://github.com/boostorg/geometry/pull/996#discussion_r876402388  

I renamed this to `copy_boundary_points` and the rest to e.g. `copy_boundary_points_of_mls`.


📁 include/boost/geometry/algorithms/detail/relate/follow_helpers.hpp

```diff
 128 | 
 129 |+ 
 130 |+ // In case it was needed in the future. Commented-out for now.
```

> Username: barendgehrels  
> Created_at: 2022-05-11 08:14:16 UTC  
> Updated_at: 2022-05-11 08:19:28 UTC  
> Url: https://github.com/boostorg/geometry/pull/996#discussion_r870004272  

But it's quite long...  
Can't we do this otherwise?

> Username: awulkiew  
> Created_at: 2022-05-18 21:35:40 UTC  
> Url: https://github.com/boostorg/geometry/pull/996#discussion_r876402450  

Removed.


📁 include/boost/geometry/algorithms/detail/relate/turns.hpp

```diff
 287 |-             >::type eq_pp_strategy_type;
 286 |+         using eq_pp_strategy_type = decltype(std::declval<Strategy>().relate(
 287 |+             detail::dummy_point(), detail::dummy_point()));
```

> Username: barendgehrels  
> Created_at: 2022-05-11 08:18:28 UTC  
> Updated_at: 2022-05-11 08:19:28 UTC  
> Url: https://github.com/boostorg/geometry/pull/996#discussion_r870008242  

Nice.


---

## Review 2 [Approved]

> Username: vissarion  
> Created_at: 2022-05-18 10:38:31 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/geometry/pull/996#pullrequestreview-976734467  

It looks good to me, thanks!

---
