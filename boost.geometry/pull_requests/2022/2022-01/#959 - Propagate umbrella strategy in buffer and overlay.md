# #959 Propagate umbrella strategy in buffer and overlay. [Merged]

> Username: awulkiew  
> Created at: 2022-01-05 22:44:08 UTC  
> Updated at: 2022-01-19 23:35:27 UTC  
> Merged at: 2022-01-19 23:35:27 UTC  
> Closed at: 2022-01-19 23:35:27 UTC  
> Url: https://github.com/boostorg/geometry/pull/959  

Furthermore:  
- pass umbrella strategy to some algorithms which were using default strategy.  
- derive buffer umbrella strategies from distance strategies.

---

## Comment 1

> Username: awulkiew  
> Created_at: 2022-01-05 22:45:44 UTC  
> Updated_at: 2022-01-05 22:46:40 UTC  
> Url: https://github.com/boostorg/geometry/pull/959#issuecomment-1006136986  

This PR also fixes an issue introduced by this change: https://github.com/boostorg/geometry/pull/951  
as described here: https://github.com/boostorg/geometry/pull/949

---

## Review 2 [Approved]

> Username: vissarion  
> Created_at: 2022-01-11 07:36:59 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/geometry/pull/959#pullrequestreview-848754263  

Thanks!

📁 include/boost/geometry/algorithms/detail/buffer/turn_in_piece_visitor.hpp

```diff
  22 | #include <boost/geometry/algorithms/detail/disjoint/point_box.hpp>
  23 | #include <boost/geometry/algorithms/detail/disjoint/box_box.hpp>
  24 |+ #include <boost/geometry/algorithms/detail/dummy_geometries.hpp>
```

> Username: vissarion  
> Created_at: 2022-01-11 07:29:32 UTC  
> Updated_at: 2022-01-11 07:36:59 UTC  
> Url: https://github.com/boostorg/geometry/pull/959#discussion_r781816235  

it is unclear to me whether dummy geometries are used in this file

> Username: awulkiew  
> Created_at: 2022-01-11 12:02:56 UTC  
> Url: https://github.com/boostorg/geometry/pull/959#discussion_r782080413  

Because they are used by algorithms and they shouldn't be used by the users, hence `detail`. If you think they should be moved somewhere. I'd be ok with it.

> Username: vissarion  
> Created_at: 2022-01-12 09:31:18 UTC  
> Updated_at: 2022-01-12 09:31:19 UTC  
> Url: https://github.com/boostorg/geometry/pull/959#discussion_r782874333  

Maybe it is cleaner to be included by the details of any algorithm using them, but I am also OK as is for now since this is a bit unrelated with this PR.


---

## Review 3 [Approved]

> Username: barendgehrels  
> Created_at: 2022-01-12 09:22:34 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/geometry/pull/959#pullrequestreview-850195114  

Thanks! Looks all good to me

---

## Comment 4

> Username: barendgehrels  
> Created_at: 2022-01-12 09:26:18 UTC  
> Url: https://github.com/boostorg/geometry/pull/959#issuecomment-1010829517  

> This PR also fixes an issue introduced by this change: #951 as described here: #949  
  
I don't know exactly what the issue was (I thought #951 fixes #949 but it's unclear to me what was wrong later). Anyway, sorry for the inconvenience and fix for fixing it and adding umbrella to buffer.

---

## Comment 5

> Username: awulkiew  
> Created_at: 2022-01-14 15:49:18 UTC  
> Updated_at: 2022-01-14 15:49:44 UTC  
> Url: https://github.com/boostorg/geometry/pull/959#issuecomment-1013240842  

@barendgehrels The issue was that there were two implementations of `get_distance_measure`. One for cartesian and the other one for everything else. These implementations were dispatched by CSTag taken from the geometry. So incorrect version of `get_distance_measure` could've been instantiated in the following cases:  
- cartesian geometry passed together with non-cartesian strategy  
- non-cartesian geometry passed together with cartesian strategy  
- geometry with undefined cs passed with cartesian strategy  
  
In all of the above cases `get_distance_measure` instantiation was incompatible with the strategy passed by the user.

---
