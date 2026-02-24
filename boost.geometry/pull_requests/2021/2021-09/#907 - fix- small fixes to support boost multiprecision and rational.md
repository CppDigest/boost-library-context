# #907 fix: small fixes to support boost multiprecision and rational [Merged]

> Username: barendgehrels  
> Created at: 2021-09-22 12:18:42 UTC  
> Updated at: 2021-09-29 10:44:54 UTC  
> Merged at: 2021-09-29 10:44:50 UTC  
> Closed at: 2021-09-29 10:44:50 UTC  
> Url: https://github.com/boostorg/geometry/pull/907  

This PR is a continuation of  #901 which was first to be merged into the other branch, and it's pr declined, but later split off

---

## Review 1 [Commented]

> Username: barendgehrels  
> Created_at: 2021-09-22 12:20:13 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/907#pullrequestreview-760834040  

📁 include/boost/geometry/algorithms/detail/overlay/sort_by_side.hpp

```diff
 322 |+                 typename geometry::coordinate_type<Point>::type,
 323 |+                 double
 324 |+             >::type;
```

> Username: barendgehrels  
> Created_at: 2021-09-22 12:20:13 UTC  
> Url: https://github.com/boostorg/geometry/pull/907#discussion_r713882764  

Required for types as `std::int16_t` to avoid overflow  
(therefore it's tested in `union_other_types.cpp` now too)


---

## Review 2 [Commented]

> Username: barendgehrels  
> Created_at: 2021-09-22 12:20:43 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/907#pullrequestreview-760834574  

📁 include/boost/geometry/algorithms/detail/sections/sectionalize.hpp

```diff
 799 |+         using gt = decltype(section.bounding_box);
 800 |+         using ct = typename geometry::coordinate_type<gt>::type;
 801 |+         ct const tolerance = ct(1) / ct(1000);
```

> Username: barendgehrels  
> Created_at: 2021-09-22 12:20:43 UTC  
> Url: https://github.com/boostorg/geometry/pull/907#discussion_r713883141  

Required for `Boost.Rational`


---

## Comment 3

> Username: barendgehrels  
> Created_at: 2021-09-22 14:26:56 UTC  
> Url: https://github.com/boostorg/geometry/pull/907#issuecomment-924984228  

Note, I get errors in `algorithms_convex_hull_robust`, unrelated to this PR.  
This might be related to the merging of `use_side_robust_predicate`

---

## Comment 4

> Username: vissarion  
> Created_at: 2021-09-23 11:56:21 UTC  
> Url: https://github.com/boostorg/geometry/pull/907#issuecomment-925741983  

> Note, I get errors in `algorithms_convex_hull_robust`, unrelated to this PR.  
> This might be related to the merging of `use_side_robust_predicate`  
  
Thanks for noticing! https://github.com/boostorg/geometry/pull/908 should fix that.

---

## Review 5 [Approved]

> Username: vissarion  
> Created_at: 2021-09-23 11:57:45 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/geometry/pull/907#pullrequestreview-761918199  

Thanks! It would be nice to have a list of all Boost multi-precision types that are supported. Are they all?

---

## Comment 6

> Username: barendgehrels  
> Created_at: 2021-09-25 11:09:34 UTC  
> Url: https://github.com/boostorg/geometry/pull/907#issuecomment-927105553  

> Thanks! It would be nice to have a list of all Boost multi-precision types that are supported. Are they all?  
  
Good point. Supported are the ones tested here. But there are more, I'll check.

---

## Comment 7

> Username: barendgehrels  
> Created_at: 2021-09-29 10:41:24 UTC  
> Url: https://github.com/boostorg/geometry/pull/907#issuecomment-930059100  

> Thanks! It would be nice to have a list of all Boost multi-precision types that are supported. Are they all?  
  
All types supported by Boost multi-precision are now included in the unit test. Those are (basically) all types but not types using other libraries (GMP, tom, ...) and not float128 (compiler dependent). Also finished the intended testcases, it was actually hard finding a test going wrong without rescaling (a good sign).  
  
Also added some additional small `zero` constants for boost::rational<short> (requires somehow more than other types)  
  
I squashed it, and will merge.

---
