# #1444 Add overlay regression case for issue 1439 [Open]

> Username: kashish2710  
> Created at: 2025-12-14 12:20:19 UTC  
> Updated at: 2025-12-23 10:14:38 UTC  
> Url: https://github.com/boostorg/geometry/pull/1444  

This PR adds a regression test case for Boost.Geometry issue #1439.  
  
– Introduces case_1439 in overlay_cases.hpp  
– The case represents two non-overlapping polygons  
– This case is intended to be used by set operation tests  
  
No algorithm behavior is changed; this only adds test data.

---

## Comment 1

> Username: kashish2710  
> Created_at: 2025-12-14 12:23:21 UTC  
> Url: https://github.com/boostorg/geometry/pull/1444#issuecomment-3650864805  

This PR adds the overlay regression case for issue #1439.

---

## Review 2 [Commented]

> Username: barendgehrels  
> Created_at: 2025-12-14 16:44:39 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1444#pullrequestreview-3575631831  

📁 test/algorithms/overlay/overlay_cases.hpp

```diff
1654 | };
1655 | 
1656 |+ // Issue 1439: non-overlapping polygons reported as full intersection
```

> Username: barendgehrels  
> Created_at: 2025-12-14 16:44:39 UTC  
> Url: https://github.com/boostorg/geometry/pull/1444#discussion_r2617311305  

But it is not called yet? Or do you still have to push something?

> Username: kashish2710  
> Created_at: 2025-12-15 04:57:31 UTC  
> Url: https://github.com/boostorg/geometry/pull/1444#discussion_r2617939681  

You’re right — the case was not called yet.  
  
I’ve now added an overlay intersection test in overlay.cpp  for case_1439  
using TEST_INTERSECTION, wrapped in  
BOOST_GEOMETRY_TEST_ENABLE_FAILING,  
and pushed the update to this PR.

> Username: kashish2710  
> Created_at: 2025-12-16 06:04:20 UTC  
> Updated_at: 2025-12-16 06:04:21 UTC  
> Url: https://github.com/boostorg/geometry/pull/1444#discussion_r2621912391  

Hi! Thanks for the review 👍    
Just checking if there’s anything else I should update before this can be merged.

> Username: barendgehrels  
> Created_at: 2025-12-16 06:57:34 UTC  
> Url: https://github.com/boostorg/geometry/pull/1444#discussion_r2622044089  

It's fine now. I added @vissarion and @tinko92 for short optional additional reviews.  
I will look tomorrow at the case itself, and get back to you.  
  
Thanks again for your quick efforts.


---

## Review 3 [Approved]

> Username: barendgehrels  
> Created_at: 2025-12-16 06:56:08 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/geometry/pull/1444#pullrequestreview-3581608690  

thanks!

---

## Review 4 [Approved]

> Username: vissarion  
> Created_at: 2025-12-23 09:04:49 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/geometry/pull/1444#pullrequestreview-3607187384  

I am OK, I left only some minor comments. Thanks @kashish2710 !   
Feel free to merge your commits into one. In any case we can still squash merge the PR.

📁 test/algorithms/overlay/overlay.cpp

```diff
 373 |+     // Issue 1439
 374 |+ // Regression test: intersection of non-overlapping polygons should be empty.
 375 |+    TEST_INTERSECTION(case_1439, 0, 0, 0.0);
```

> Username: vissarion  
> Created_at: 2025-12-23 09:02:31 UTC  
> Updated_at: 2025-12-23 09:04:49 UTC  
> Url: https://github.com/boostorg/geometry/pull/1444#discussion_r2642464698  

maybe issue_1439 is more descriptive

---

📁 test/algorithms/overlay/overlay.cpp

```diff
 372 |+     #ifdef BOOST_GEOMETRY_TEST_ENABLE_FAILING
 373 |+     // Issue 1439
 374 |+ // Regression test: intersection of non-overlapping polygons should be empty.
```

> Username: vissarion  
> Created_at: 2025-12-23 09:02:45 UTC  
> Updated_at: 2025-12-23 09:04:49 UTC  
> Url: https://github.com/boostorg/geometry/pull/1444#discussion_r2642465499  

please fix indentation

---

📁 test/algorithms/overlay/overlay.cpp

```diff
 371 |+     
 372 |+     #ifdef BOOST_GEOMETRY_TEST_ENABLE_FAILING
 373 |+     // Issue 1439
```

> Username: vissarion  
> Created_at: 2025-12-23 09:03:14 UTC  
> Updated_at: 2025-12-23 09:04:49 UTC  
> Url: https://github.com/boostorg/geometry/pull/1444#discussion_r2642466621  

this is not needed if the name of the test is changed


---

## Comment 5

> Username: kashish2710  
> Created_at: 2025-12-23 09:59:20 UTC  
> Url: https://github.com/boostorg/geometry/pull/1444#issuecomment-3686011197  

Thanks @vissarion  for the review and suggestions!  
I’ve renamed the test to issue_1439, fixed the indentation, and removed the redundant comment.  
Appreciate the feedback. 🚀

---

## Review 6 [Approved]

> Username: tinko92  
> Created_at: 2025-12-23 10:14:38 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/geometry/pull/1444#pullrequestreview-3607454963  

Looks good to me!

---
