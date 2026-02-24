# #1441 Add regression test for non-overlapping polygon intersection (issue #1439) [Open]

> Username: kashish2710  
> Created at: 2025-12-13 14:06:22 UTC  
> Updated at: 2025-12-14 10:07:08 UTC  
> Url: https://github.com/boostorg/geometry/pull/1441  

This PR adds a minimal regression test reproducing issue #1439.  
  
The test shows that `boost::geometry::intersection` currently produces  
a non-empty result for two polygons that do not overlap.  
  
The test fails on Boost 1.89 (tested on Linux / Google Colab),  
confirming that the reported behavior is still present.  
  
Once the underlying issue is fixed, this test will help ensure  
that the behavior does not regress in future changes.  
  
<img width="1255" height="642" alt="Screenshot 2025-12-13 at 4 32 31 PM" src="https://github.com/user-attachments/assets/38afb46d-46bd-4be5-8b0a-50ecdd1d6fe1" />  
  
Still reproducible on Boost 1.89 (Linux).  
See attached output showing non-empty intersection for non-overlapping polygons.

---

## Comment 1

> Username: kashish2710  
> Created_at: 2025-12-13 14:18:34 UTC  
> Url: https://github.com/boostorg/geometry/pull/1441#issuecomment-3649476157  

The test is intentionally minimal to clearly reproduce the issue.  
I can adapt it to the Boost test framework if preferred.

---

## Review 2 [Commented]

> Username: barendgehrels  
> Created_at: 2025-12-14 10:06:20 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1441#pullrequestreview-3574946373  

📁 test/algorithms/intersection/issue_1439.cpp

```diff
  37 |+     // Expected: no intersection
  38 |+     return output.empty() ? 0 : 1;
  39 |+ }
```

> Username: barendgehrels  
> Created_at: 2025-12-14 10:06:20 UTC  
> Url: https://github.com/boostorg/geometry/pull/1441#discussion_r2616892271  

Thanks a lot. But yes, please adapt it to our current framework.  
  
The cases themselves can go to:  
https://github.com/boostorg/geometry/blob/develop/test/algorithms/overlay/overlay_cases.hpp#L1273  
  
The test can go to:  
https://github.com/boostorg/geometry/blob/develop/test/algorithms/set_operations/intersection/intersection.cpp#L297  
  
These tests test on area, so the expected area should be `0.0`   
  
The test will then fail. So you can put the test between  
`#ifdef BOOST_GEOMETRY_TEST_ENABLE_FAILING`  
and  
`#endif`


---

## Review 3 [Changes requested]

> Username: barendgehrels  
> Created_at: 2025-12-14 10:07:08 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/geometry/pull/1441#pullrequestreview-3574946708  

As you mentioned, and I added in the comment,  
better to have it in the framework.

---
