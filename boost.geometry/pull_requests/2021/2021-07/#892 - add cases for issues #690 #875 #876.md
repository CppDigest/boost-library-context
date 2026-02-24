# #892 [test] add cases for issues #690 #875 #876 [Merged]

> Username: barendgehrels  
> Created at: 2021-07-28 13:48:10 UTC  
> Updated at: 2021-08-04 12:07:04 UTC  
> Merged at: 2021-08-04 12:07:01 UTC  
> Closed at: 2021-08-04 12:07:01 UTC  
> Url: https://github.com/boostorg/geometry/pull/892  

This adds cases for issues #690 , #875 and #876 which I all closed today  
Without rescaling they are all fine.  
  
With rescaling and #690 there is something weird in the validity check, it might be related to the (robust) side_by_triangle determination too (see comment of @tinko92 in that issue). Therefore I changed the check_validity code in the test code too.

---

## Review 1 [Commented]

> Username: barendgehrels  
> Created_at: 2021-07-28 13:49:41 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/892#pullrequestreview-717025604  

📁 test/algorithms/set_operations/difference/difference.cpp

```diff
 635 |     // Many of the failures are self-intersection points.
 630 |-     BoostGeometryWriteExpectedFailures(12, 10, 17, 12);
 636 |+     BoostGeometryWriteExpectedFailures(19, 10, 17, 12);
```

> Username: barendgehrels  
> Created_at: 2021-07-28 13:49:40 UTC  
> Updated_at: 2021-07-28 13:49:41 UTC  
> Url: https://github.com/boostorg/geometry/pull/892#discussion_r678319576  

Side note: adding one case can indeed cause 7 extra reported issues (on number of geometries, area, validity, for a-b and b-a, s, etc)


---

## Review 2 [Commented]

> Username: awulkiew  
> Created_at: 2021-07-30 11:20:54 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/892#pullrequestreview-718977029  

📁 test/algorithms/check_validity.hpp

```diff
  25 |+                   << "] is not considered as valid ("
  26 |+                   << message << ") this can cause that output is invalid: "
  27 |+                   << case_id << std::endl;
```

> Username: awulkiew  
> Created_at: 2021-07-30 11:20:53 UTC  
> Updated_at: 2021-07-30 11:20:54 UTC  
> Url: https://github.com/boostorg/geometry/pull/892#discussion_r679849578  

You should probably use `BOOST_TEST_MESSAGE` here instead.

> Username: barendgehrels  
> Created_at: 2021-08-04 12:06:25 UTC  
> Url: https://github.com/boostorg/geometry/pull/892#discussion_r682552905  

I don't know it yet, thanks.  
I'll merge this (it's consistent to previous behavior) but will look at this in a later phase.


---

## Review 3 [Approved]

> Username: vissarion  
> Created_at: 2021-07-30 13:37:51 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/geometry/pull/892#pullrequestreview-719086493  

Thanks, I am OK to merge these.

📁 test/algorithms/set_operations/union/union.cpp

```diff
 431 |     TEST_UNION_REV(issue_566_b, 1, 0, -1, 214.3728);
 432 | 
 433 |+ #if ! defined(BOOST_GEOMETRY_USE_RESCALING) || defined(BOOST_GEOMETRY_TEST_FAILURES)
```

> Username: vissarion  
> Created_at: 2021-07-30 13:37:35 UTC  
> Updated_at: 2021-07-30 13:37:51 UTC  
> Url: https://github.com/boostorg/geometry/pull/892#discussion_r679931666  

I tested it with `side_robust` i.e. merging with https://github.com/boostorg/geometry/pull/873 and those tests are still failing, so probably not a side issue, and should be further investigated.

> Username: barendgehrels  
> Created_at: 2021-08-04 12:06:49 UTC  
> Updated_at: 2021-08-04 12:06:50 UTC  
> Url: https://github.com/boostorg/geometry/pull/892#discussion_r682553132  

Good to know, thanks


---
