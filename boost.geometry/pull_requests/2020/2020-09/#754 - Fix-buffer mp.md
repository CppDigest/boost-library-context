# #754 Fix/buffer mp [Merged]

> Username: barendgehrels  
> Created at: 2020-09-23 12:04:05 UTC  
> Updated at: 2020-10-15 23:38:05 UTC  
> Merged at: 2020-09-30 08:18:33 UTC  
> Closed at: 2020-09-30 08:18:33 UTC  
> Url: https://github.com/boostorg/geometry/pull/754  

This reverts the removal of monotonic sections in previous PR, because it is indeed for optimization as Adam mentioned. Difference in performance is around 20%  
  
Instead it fixes the type used for partitioning, and extends the type checking to various places where partitioning is used in buffer (and in assign_parents).  
  
For the type checking I added a file (and it uses `static_assert` and `std::is_same` now :-) )

---

## Review 1 [Approved]

> Username: vissarion  
> Created_at: 2020-09-23 12:31:11 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/geometry/pull/754#pullrequestreview-494607188  

That looks ok to me.

---

## Review 2 [Commented]

> Username: awulkiew  
> Created_at: 2020-09-23 14:19:26 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/754#pullrequestreview-494712546  

📁 include/boost/geometry/core/check_for_same_type.hpp

```diff
  35 |+           >::value, "Coordinate types in geometries should be the same");
  36 |+     }
  37 |+ };
```

> Username: awulkiew  
> Created_at: 2020-09-23 14:19:26 UTC  
> Updated_at: 2020-09-30 08:16:56 UTC  
> Url: https://github.com/boostorg/geometry/pull/754#discussion_r493631761  

4 problems with that:  
- this tool static asserts for the same `coordinate_type` not the same type  
- we already have similar tools for checking assertions about dimensions placed in a file where `geometry::dimension` is defined [1]  
- these tools are `inline` free functions  
- it should've been `constexpr` in order to allow using it in other `constexpr` functions  
  
So I think this should be moved to `core/coordinate_type.hpp`, be called `assert_same_coordinate_type` or `assert_coordinate_type_equal` etc. and be implemented as a `constexpr inline` free function just like dimension utils.  
  
[1] https://github.com/boostorg/geometry/blob/develop/include/boost/geometry/core/coordinate_dimension.hpp#L120

> Username: barendgehrels  
> Created_at: 2020-09-23 18:27:44 UTC  
> Updated_at: 2020-09-30 08:16:56 UTC  
> Url: https://github.com/boostorg/geometry/pull/754#discussion_r493801079  

Sounds good, thank you. I'll update the PR.

> Username: barendgehrels  
> Created_at: 2020-09-24 07:11:07 UTC  
> Updated_at: 2020-09-30 08:16:56 UTC  
> Url: https://github.com/boostorg/geometry/pull/754#discussion_r494087153  

It's updated


---

## Review 3 [Commented]

> Username: awulkiew  
> Created_at: 2020-09-24 15:21:42 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/754#pullrequestreview-495687946  

📁 include/boost/geometry/algorithms/detail/buffer/get_piece_turns.hpp

```diff
 238 |+                     section2.directions[1], section2.bounding_box, section1.bounding_box);
 239 |+         move_end_iterator<1>(it2_first, it2_beyond,
 240 |+                     section2.directions[1], section2.bounding_box, section1.bounding_box);
```

> Username: awulkiew  
> Created_at: 2020-09-24 15:21:42 UTC  
> Updated_at: 2020-09-30 08:16:56 UTC  
> Url: https://github.com/boostorg/geometry/pull/754#discussion_r494406408  

So this is needed after all? What is the reason of reverting the last change, performance?

> Username: barendgehrels  
> Created_at: 2020-09-24 15:35:35 UTC  
> Updated_at: 2020-09-30 08:16:56 UTC  
> Url: https://github.com/boostorg/geometry/pull/754#discussion_r494416969  

Yes, see description. It was ~20% slower in the bulk performance test.


---

## Review 4 [Commented]

> Username: awulkiew  
> Created_at: 2020-09-24 15:25:12 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/754#pullrequestreview-495691371  

📁 include/boost/geometry/algorithms/detail/overlay/get_distance_measure.hpp

```diff
  57 |     // algorithm returns positive value if it is located on the left side.
  64 |-     bool is_positive() const { return measure > 0.0; }
  58 |+     bool is_positive() const { return measure > zero(); }
```

> Username: awulkiew  
> Created_at: 2020-09-24 15:25:12 UTC  
> Updated_at: 2020-09-30 08:16:56 UTC  
> Url: https://github.com/boostorg/geometry/pull/754#discussion_r494409033  

Why not simply `T(0)` which would be shorter, more clear IMO and not require an additional function?

> Username: barendgehrels  
> Created_at: 2020-09-30 08:18:12 UTC  
> Url: https://github.com/boostorg/geometry/pull/754#discussion_r497326470  

It's updated.


---
