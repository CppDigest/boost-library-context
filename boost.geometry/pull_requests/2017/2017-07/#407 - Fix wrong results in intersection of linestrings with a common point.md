# #407 Fix wrong results in intersection of linestrings with a common point [Merged]

> Username: vissarion  
> Created at: 2017-07-04 07:26:22 UTC  
> Updated at: 2018-01-22 22:18:33 UTC  
> Merged at: 2017-07-04 13:55:27 UTC  
> Closed at: 2017-07-04 13:55:27 UTC  
> Url: https://github.com/boostorg/geometry/pull/407  

This PR proposes a fix for the bug related to ticket https://svn.boost.org/trac10/ticket/13057  
  
It is about producing wrong results when intersecting two linestrings with a common point.

---

## Review 1 [Commented]

> Username: awulkiew  
> Created_at: 2017-07-04 13:52:57 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/407#pullrequestreview-47871067  

📁 include/boost/geometry/algorithms/detail/disjoint/box_box.hpp

```diff
 122 |             calc_t const diff_min = math::longitude_distance_unsigned<units_t>(b1_min, b2_min);
 123 |             calc_t const b2_min_transl = b1_min + diff_min; // always right of b1_min
 124 |+             calc_t b2_max_transl = b2_min_transl - constants::period() + diff2;;
```

> Username: awulkiew  
> Created_at: 2017-07-04 13:52:57 UTC  
> Updated_at: 2017-07-04 13:52:58 UTC  
> Url: https://github.com/boostorg/geometry/pull/407#discussion_r125478460  

Double `;`, but I can fix it later.


---

## Comment 2

> Username: awulkiew  
> Created_at: 2017-07-04 13:54:41 UTC  
> Url: https://github.com/boostorg/geometry/pull/407#issuecomment-312884161  

Thanks!

---
