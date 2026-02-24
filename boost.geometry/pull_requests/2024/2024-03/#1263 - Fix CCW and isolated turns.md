# #1263 Fix CCW and isolated turns [Merged]

> Username: vissarion  
> Created at: 2024-03-26 20:20:46 UTC  
> Updated at: 2024-04-01 09:56:33 UTC  
> Merged at: 2024-04-01 09:56:30 UTC  
> Closed at: 2024-04-01 09:56:30 UTC  
> Url: https://github.com/boostorg/geometry/pull/1263  

Fixes issues #1231, #1244 (and a few more test cases). I want to target 1.85 if possible.

---

## Review 1 [Approved]

> Username: barendgehrels  
> Created_at: 2024-03-26 20:58:54 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/geometry/pull/1263#pullrequestreview-1961721232  

📁 include/boost/geometry/algorithms/detail/overlay/traversal_switch_detector.hpp

```diff
 398 |+             constexpr auto order2 = geometry::point_order<Geometry2>::value;
 399 |+             constexpr bool reverse2 = (order2 == boost::geometry::counterclockwise)
 400 |+                 ? ! Reverse2 : Reverse2;
```

> Username: barendgehrels  
> Created_at: 2024-03-26 20:57:21 UTC  
> Updated_at: 2024-03-26 20:58:54 UTC  
> Url: https://github.com/boostorg/geometry/pull/1263#discussion_r1540103121  

👍 great fix, thanks a lot


📁 test/algorithms/set_operations/intersection/intersection.cpp

```diff
 317 |         ut_settings settings;
 321 |-         settings.set_test_validity(! is_ccw);
 318 |+         settings.set_test_validity(true);
```

> Username: barendgehrels  
> Created_at: 2024-03-26 20:58:09 UTC  
> Updated_at: 2024-03-26 20:58:54 UTC  
> Url: https://github.com/boostorg/geometry/pull/1263#discussion_r1540103957  

This works - but you can also remove the settings and just call `TEST_INTERSECTION` (here and below)


---
