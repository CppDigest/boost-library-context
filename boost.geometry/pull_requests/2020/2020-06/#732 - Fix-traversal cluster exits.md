# #732 Fix/traversal cluster exits [Merged]

> Username: barendgehrels  
> Created at: 2020-06-24 10:52:18 UTC  
> Updated at: 2020-07-01 08:15:09 UTC  
> Merged at: 2020-07-01 08:15:04 UTC  
> Closed at: 2020-07-01 08:15:04 UTC  
> Url: https://github.com/boostorg/geometry/pull/732  

* Fix some cases in buffer which went wrong without rescaling  
* Therefore extracted cluster_exits functionality (was already there, but in traversal. Now in external file)  
* Clean up some defines in robustness test and write test configuration

---

## Review 1 [Commented]

> Username: barendgehrels  
> Created_at: 2020-06-24 10:53:52 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/732#pullrequestreview-436536610  

📁 include/boost/geometry/algorithms/detail/overlay/traversal.hpp

```diff
 761 |-     // arriving at the first turn, first turn points to second turn,
 762 |-     // second turn points further.
 763 |-     inline bool select_turn_from_cluster_linked(signed_size_type& turn_index,
```

> Username: barendgehrels  
> Created_at: 2020-06-24 10:53:51 UTC  
> Updated_at: 2020-06-24 10:53:52 UTC  
> Url: https://github.com/boostorg/geometry/pull/732#discussion_r444810581  

this functionality is now moved to `cluster_exit`, a struct is made, and there is one additional condition (the fix)


---

## Review 2 [Commented]

> Username: barendgehrels  
> Created_at: 2020-06-24 10:54:25 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/732#pullrequestreview-436536981  

📁 test/algorithms/buffer/buffer_multi_polygon.cpp

```diff
 545 | #if defined(BOOST_GEOMETRY_TEST_FAILURES)
 554 |-     BoostGeometryWriteExpectedFailures(1, 9);
 546 |+     BoostGeometryWriteExpectedFailures(1, 5);
```

> Username: barendgehrels  
> Created_at: 2020-06-24 10:54:24 UTC  
> Updated_at: 2020-06-24 10:54:25 UTC  
> Url: https://github.com/boostorg/geometry/pull/732#discussion_r444810888  

It effectively fixes 2 cases


---

## Review 3 [Approved]

> Username: vissarion  
> Created_at: 2020-06-24 13:45:18 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/geometry/pull/732#pullrequestreview-436660994  

Thanks! Look ok to me.

---
