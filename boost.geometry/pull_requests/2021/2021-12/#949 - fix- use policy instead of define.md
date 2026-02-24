# #949 fix: use policy instead of define [Merged]

> Username: barendgehrels  
> Created at: 2021-12-08 13:27:34 UTC  
> Updated at: 2021-12-15 18:29:12 UTC  
> Merged at: 2021-12-15 09:29:38 UTC  
> Closed at: 2021-12-15 09:29:38 UTC  
> Url: https://github.com/boostorg/geometry/pull/949  

This PR removes the define `BOOST_GEOMETRY_USE_RESCALING` from `get_turn_info.hpp` and replaces it with a verification policy, which is defined for areal/areal (if rescaling is removed) but not for areal/linear and linear/linear because they did not do these verifications before.  
  
After this, all tests from set_operations pass when rescaling is turned off.

---

## Review 1 [Approved]

> Username: vissarion  
> Created_at: 2021-12-08 15:26:49 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/geometry/pull/949#pullrequestreview-826576933  

Thanks! I am OK with merging modulo some minor remarks.

📁 test/algorithms/set_operations/intersection/intersection_linear_linear.cpp

```diff
1277 |-          from_wkt<ML>("MULTILINESTRING()"),
1278 |-   #else
1279 |-          from_wkt<ML>("MULTILINESTRING((1.87562 6.68515,1.87562 6.68515),(1.60494 6,1.60494 6),(1.18124 4.9275,1.18124 4.9275),(1.00439 4.47984,1.00439 4.47984),(0.91526 4.25422,0.91526 4.25422),(0.729883 3.78498,0.729883 3.78498),(0.614728 3.49349,0.614728 3.49349),(0.591231 3.43401,0.591231 3.43401),(0.412281 2.98104,0.412281 2.98104),(0.358522 2.84496,0.358522 2.84496),(0.235887 2.53454,0.235887 2.53454),(0.10749 2.20953,0.10749 2.20953),(0.0954852 2.17914,0.0954852 2.17914),(-0.7654 8.88178e-16,-0.7654 8.88178e-16),(0.623783 0.722855,0.623783 0.722855),(1.01466 0.926246,1.01466 0.926246),(1.70073 1.28324,1.70073 1.28324),(1.87531 1.37408,1.87531 1.37408),(2.09493 1.48836,2.09493 1.48836),(2.3516 1.62191,2.3516 1.62191),(2.50083 1.69957,2.50083 1.69957),(3.06464 1.99294,3.06464 1.99294),(4.41962 2.698,4.41962 2.698),(4.87386 2.93436,4.87386 2.93436),(5 3,5 3),(5 3,5 3),(0.0484053 -0.635122,0.0484053 -0.635122),(0.535994 0.677175,0.535994 0.677175),(0.623165 0.911787,0.623165 0.911787),(0.92255 1.71755,0.92255 1.71755),(1.01168 1.95744,1.01168 1.95744),(1.03214 2.01251,1.03214 2.01251),(1.12031 2.2498,1.12031 2.2498),(1.36441 2.90677,1.36441 2.90677),(1.38821 2.97083,1.38821 2.97083),(1.39905 3,1.39905 3),(1.40063 3.00424,1.40063 3.00424),(1.4995 3.27036,1.4995 3.27036),(1.59592 3.52985,1.59592 3.52985),(1.99437 4.60225,1.99437 4.60225),(2.51371 6,2.51371 6),(2.54193 6.07595,2.54193 6.07595),(4 6,4 6),(4 4.74286,4 4.74286),(4 4.33333,4 4.33333),(4 4.07748,4 4.07748),(4 3.8,4 3.8),(4 3.8,4 3.8),(4 3.6,4 3.6),(4 3.5,4 3.5),(4 3.03179,4 3.03179),(4 3,4 3),(4 2.9435,4 2.9435),(4 2.82162,4 2.82162),(4 2.47965,4 2.47965),(4 1.72377,4 1.72377),(4 1.38014,4 1.38014),(3.43534 0.441146,3.43534 0.441146),(2.06005 1.27206,2.06005 1.27206),(1.88383 1.37852,1.88383 1.37852),(1.8713 1.38609,1.8713 1.38609),(1.01183 1.90535,1.01183 1.90535),(0.72677 2.07758,0.72677 2.07758),(0.619143 2.1426,0.619143 2.1426),(-0.532243 2.83823,-0.532243 2.83823),(-0.789427 2.99361,-0.789427 2.99361),(-0.756651 3.30964,-0.756651 3.30964),(-0.706503 3.66784,-0.706503 3.66784),(-0.705343 3.67612,-0.705343 3.67612),(0.502294 7.64221,0.502294 7.64221),(0.601362 7.58336,0.601362 7.58336),(0.641699 7.5594,0.641699 7.5594),(1.43457 7.08839,1.43457 7.08839),(3.26673 6,3.26673 6),(5.09496 4.91395,5.09496 4.91395),(5.26567 4.81254,5.26567 4.81254),(6.47672 4.09311,6.47672 4.09311),(8.18862 3.07616,8.18862 3.07616),(8.49103 2.89652,8.49103 2.89652),(8.5655 2.85228,8.5655 2.85228),(9.98265 0.00543606,9.98265 0.00543606),(9.09826 -100.516,9.09826 -100.516),(7.08745 -329.067,7.08745 -329.067),(5.06428 -559.024,5.06428 -559.024),(3.23365 -767.097,3.23365 -767.097),(3.16036 -775.427,3.16036 -775.427))"),
```

> Username: vissarion  
> Created_at: 2021-12-08 15:23:47 UTC  
> Updated_at: 2021-12-08 15:26:49 UTC  
> Url: https://github.com/boostorg/geometry/pull/949#discussion_r764966980  

why is this case removed?

> Username: barendgehrels  
> Created_at: 2021-12-08 16:06:04 UTC  
> Url: https://github.com/boostorg/geometry/pull/949#discussion_r765006088  

It's not removed, the specific expectation for `if-rescaling-is-of` is removed


📁 include/boost/geometry/algorithms/detail/overlay/get_turn_info.hpp

```diff
  67 |+ {
  68 |+     static bool const use_side_verification = false;
  69 |+     static bool const use_start_turn = false;
```

> Username: vissarion  
> Created_at: 2021-12-08 15:26:12 UTC  
> Updated_at: 2021-12-08 15:26:50 UTC  
> Url: https://github.com/boostorg/geometry/pull/949#discussion_r764969361  

should `use_start_turn`, `use_handle_as_touch`, `use_handle_as_equal` and `use_handle_imperfect_touch` be merged to one?

> Username: barendgehrels  
> Created_at: 2021-12-08 16:07:20 UTC  
> Url: https://github.com/boostorg/geometry/pull/949#discussion_r765007194  

Good question. I was not sure yet. Yes, we could merge them all - but basically linestrings could profit from these too (to be investigated), and these settings make it easy to gradually change them

> Username: barendgehrels  
> Created_at: 2021-12-15 09:29:07 UTC  
> Url: https://github.com/boostorg/geometry/pull/949#discussion_r769430160  

I'll keep it for now, it's internal, we can revisit it later.


---

## Comment 2

> Username: barendgehrels  
> Created_at: 2021-12-15 09:29:33 UTC  
> Url: https://github.com/boostorg/geometry/pull/949#issuecomment-994569737  

Merging it, @awulkiew if you have concerns they can be addressed later

---

## Comment 3

> Username: barendgehrels  
> Created_at: 2021-12-15 12:56:32 UTC  
> Url: https://github.com/boostorg/geometry/pull/949#issuecomment-994765961  

I get mails from `circleci` regarding a failure in `cs_undefined`  
Now checking.

---

## Comment 4

> Username: awulkiew  
> Created_at: 2021-12-15 18:18:44 UTC  
> Updated_at: 2021-12-15 18:29:12 UTC  
> Url: https://github.com/boostorg/geometry/pull/949#issuecomment-995046778  

@barendgehrels Is this: https://github.com/boostorg/geometry/commit/ca42385d3b5a38b4b459af5b22d20013c72a89de the fix for circleci and cs_undefined? If that's the case then the fix is incorrect.  
  
The CSTag should be taken from the strategy rather than from the geometries inside `get_distance_measure` here:  
https://github.com/boostorg/geometry/blob/ca42385d3b5a38b4b459af5b22d20013c72a89de/include/boost/geometry/algorithms/detail/overlay/get_distance_measure.hpp#L111  
  
In general this utility is CS specific and technically should be made into strategy. But if it's convenient for you passing `cs_tag` from umbrella strategy should be enough.

---
