# #1252 [fix] assign next turn [Merged]

> Username: barendgehrels  
> Created at: 2024-02-28 21:41:35 UTC  
> Updated at: 2024-03-13 09:29:45 UTC  
> Merged at: 2024-03-13 09:29:38 UTC  
> Closed at: 2024-03-13 09:29:38 UTC  
> Url: https://github.com/boostorg/geometry/pull/1252  

Fixes issue #1250

---

## Review 1 [Commented]

> Username: barendgehrels  
> Created_at: 2024-02-28 21:43:12 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1252#pullrequestreview-1907484539  

📁 include/boost/geometry/algorithms/detail/overlay/enrich_intersection_points.hpp

```diff
 137 |+         if (check_consecutive_turns
 138 |+             && indexed.turn_index == operations[next_index].turn_index
 139 |+             && op.seg_id == next_operation().seg_id)
```

> Username: barendgehrels  
> Created_at: 2024-02-28 21:43:12 UTC  
> Url: https://github.com/boostorg/geometry/pull/1252#discussion_r1506700419  

This last extra condition is the real fix.  
  
The rest is refactored, because `++next` unconditionally (at the end) was suspicious,  
the pointer was also increased before, under some conditions.  
Therefore we use `next_index` now with circular behavior (like done in the same source, further on)


---

## Review 2 [Commented]

> Username: barendgehrels  
> Created_at: 2024-02-28 21:45:46 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1252#pullrequestreview-1907488199  

📁 test/algorithms/buffer/buffer_linestring.cpp

```diff
 350 |+         using bg::strategy::buffer::end_flat;
 351 |+         test_one<linestring, polygon>("issue_1250", issue_1250, join_miter(5), end_flat(), 8.39277, 0.5);
 352 |+         test_one<linestring, polygon>("issue_1250_rev", issue_1250_rev, join_miter(5), end_flat(), 8.39277, 0.5);
```

> Username: barendgehrels  
> Created_at: 2024-02-28 21:45:46 UTC  
> Url: https://github.com/boostorg/geometry/pull/1252#discussion_r1506702707  

This cases is fixed. See the issue for a picture.  
It was surprisingly simple case - and therefore I was surprised that it was failing, and similar other cases were not failing. The cause was that it increased the next-index, because there was only one turn in the outer ring, and therefore it pointed to the wrong next turn.


---

## Review 3 [Commented]

> Username: barendgehrels  
> Created_at: 2024-02-28 21:50:20 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1252#pullrequestreview-1907494489  

📁 include/boost/geometry/algorithms/detail/overlay/enrich_intersection_points.hpp

```diff
 495 |-         for (auto const& op : pair.second)
 496 |-         {
 497 |-             std::cout << op.turn_index << " " << op.operation_index << std::endl;
```

> Username: barendgehrels  
> Created_at: 2024-02-28 21:50:20 UTC  
> Updated_at: 2024-02-28 21:50:21 UTC  
> Url: https://github.com/boostorg/geometry/pull/1252#discussion_r1506706834  

this was without any context and not useful


---

## Review 4 [Commented]

> Username: barendgehrels  
> Created_at: 2024-02-28 21:50:47 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1252#pullrequestreview-1907495163  

📁 test/algorithms/buffer/CMakeLists.txt

```diff
   8 |     buffer
   9 |     buffer_gc
  10 |+     buffer_variable_width
```

> Username: barendgehrels  
> Created_at: 2024-02-28 21:50:47 UTC  
> Url: https://github.com/boostorg/geometry/pull/1252#discussion_r1506707317  

this is the case of last PR, but was not in the cmake yet because that PR was made before


---

## Review 5 [Approved]

> Username: vissarion  
> Created_at: 2024-03-12 12:18:36 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/geometry/pull/1252#pullrequestreview-1930895939  

Thanks LGTM!

---
