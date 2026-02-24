# #1352 test: add set_ops_areal_areal which tests all operations [Merged]

> Username: barendgehrels  
> Created at: 2024-12-06 19:33:59 UTC  
> Updated at: 2025-03-18 13:18:28 UTC  
> Merged at: 2024-12-10 18:02:45 UTC  
> Closed at: 2024-12-10 18:02:45 UTC  
> Url: https://github.com/boostorg/geometry/pull/1352  



---

## Review 1 [Commented]

> Username: barendgehrels  
> Created_at: 2024-12-06 19:35:01 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1352#pullrequestreview-2485709660  

📁 test/algorithms/set_operations/difference/difference.cpp

```diff
 641 |-         ut_settings settings;
 642 |-         settings.validity_of_sym = false;
 643 |-         TEST_DIFFERENCE_WITH(issue_1293, 1, 1.40999, 1, 2.318951, 2, settings);
```

> Username: barendgehrels  
> Created_at: 2024-12-06 19:35:01 UTC  
> Url: https://github.com/boostorg/geometry/pull/1352#discussion_r1873886024  

this is fixed now


---

## Review 2 [Commented]

> Username: barendgehrels  
> Created_at: 2024-12-06 19:36:31 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1352#pullrequestreview-2485712263  

📁 test/algorithms/set_operations/intersection/intersection.cpp

```diff
 305 | 
 306 |-     TEST_INTERSECTION(issue_1226, 1, -1, 0.00036722862);
 306 |     TEST_INTERSECTION(issue_1229, 0, -1, 0);
```

> Username: barendgehrels  
> Created_at: 2024-12-06 19:36:31 UTC  
> Url: https://github.com/boostorg/geometry/pull/1352#discussion_r1873887566  

This one `issue_1229` is kept because it fails for difference (wrong result), this was not tested before.  
The new test can ignore validity, but not (yet) wrong results.


---

## Review 3 [Commented]

> Username: barendgehrels  
> Created_at: 2024-12-06 19:37:05 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1352#pullrequestreview-2485713094  

📁 test/algorithms/set_operations/intersection/intersection.cpp

```diff
 319 |- 
 320 |-     TEST_INTERSECTION(issue_1345_a, 1, -1, 0.00062682687);
 321 |-     TEST_INTERSECTION(issue_1345_b, 1, -1, 0.010896761);
```

> Username: barendgehrels  
> Created_at: 2024-12-06 19:37:04 UTC  
> Updated_at: 2024-12-06 19:37:05 UTC  
> Url: https://github.com/boostorg/geometry/pull/1352#discussion_r1873888053  

The rest (of the `issue_12*` and `13*` series) are moved to the new suite


---

## Review 4 [Commented]

> Username: barendgehrels  
> Created_at: 2024-12-06 19:37:54 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1352#pullrequestreview-2485714354  

📁 test/algorithms/set_operations/set_ops_areal_areal.cpp

```diff
 102 |+     auto const balance_d1 = (area_union - area2) - area_diff1;
 103 |+     auto const balance_d2 = (area_union - area1) - area_diff2;
 104 |+     auto const balance_sym = (area_diff1 + area_diff2) - area_sym_diff;
```

> Username: barendgehrels  
> Created_at: 2024-12-06 19:37:54 UTC  
> Updated_at: 2024-12-06 19:37:55 UTC  
> Url: https://github.com/boostorg/geometry/pull/1352#discussion_r1873888769  

This math is also used in robustness tests.  
It avoids specifying area for every test case.


---

## Review 5 [Commented]

> Username: barendgehrels  
> Created_at: 2024-12-06 19:41:37 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1352#pullrequestreview-2485721378  

📁 test/algorithms/overlay/multi_overlay_cases.hpp

```diff
1601 | };
1602 | 
1603 |+ static std::string issue_1350_comment[2] =
```

> Username: barendgehrels  
> Created_at: 2024-12-06 19:41:37 UTC  
> Url: https://github.com/boostorg/geometry/pull/1352#discussion_r1873892888  

@tinko92 this is the issue you mentioned in #1350


---

## Review 6 [Commented]

> Username: barendgehrels  
> Created_at: 2024-12-09 17:45:04 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1352#pullrequestreview-2489547971  

📁 test/algorithms/set_operations/set_ops_areal_areal.cpp

```diff
 204 |+     TEST_CASE(issue_1299);
 205 |+ 
 206 |+     TEST_CASE(issue_1326);
```

> Username: barendgehrels  
> Created_at: 2024-12-09 17:45:04 UTC  
> Updated_at: 2024-12-09 17:45:27 UTC  
> Url: https://github.com/boostorg/geometry/pull/1352#discussion_r1876422464  

So, in summary, this one-line test-case replaces four lines with areas, spread over 3 files:  
  
```  
   TEST_UNION(issue_1326, 1, 0, -1, 23.201);  
   TEST_UNION_REV(issue_1326, 1, 0, -1, 23.201);  
   TEST_INTERSECTION(issue_1326, 1, -1, 16.4844);  
   TEST_DIFFERENCE(issue_1326, 3, 6.7128537626409130468, 6, 0.00372806966532758478, 9);  
```


---

## Review 7 [Approved]

> Username: vissarion  
> Created_at: 2024-12-10 13:40:27 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/geometry/pull/1352#pullrequestreview-2492360608  

Thanks! AFAIU there is no new test added (apart from the one related to https://github.com/boostorg/geometry/pull/1350) so this is a refactoring on tests for simplicity.

---

## Comment 8

> Username: barendgehrels  
> Created_at: 2024-12-10 18:02:15 UTC  
> Url: https://github.com/boostorg/geometry/pull/1352#issuecomment-2532481020  

> Thanks! AFAIU there is no new test added (apart from the one related to #1350) so this is a refactoring on tests for simplicity.  
  
Indeed. So I could have used the tag `refactor` but usually I reserve that for real code. Thanks for your review!

---
