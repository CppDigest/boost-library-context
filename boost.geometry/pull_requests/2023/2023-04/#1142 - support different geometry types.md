# #1142 [fix] support different geometry types [Merged]

> Username: barendgehrels  
> Created at: 2023-04-23 15:52:38 UTC  
> Updated at: 2023-06-16 14:03:41 UTC  
> Merged at: 2023-05-08 18:31:01 UTC  
> Closed at: 2023-05-08 18:31:01 UTC  
> Url: https://github.com/boostorg/geometry/pull/1142  

This fixes #1139 , and more required code changes.  
  
Including unit test.

---

## Review 1 [Commented]

> Username: barendgehrels  
> Created_at: 2023-04-23 15:54:11 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1142#pullrequestreview-1396930304  

📁 include/boost/geometry/algorithms/detail/overlay/get_turn_info.hpp

```diff
1038 |+        auto const dm = arrival_p == 1
1039 |+               ? fun::distance_measure(info.intersections[1], range_q.at(1))
1040 |+               : fun::distance_measure(info.intersections[1], range_p.at(1));
```

> Username: barendgehrels  
> Created_at: 2023-04-23 15:54:10 UTC  
> Updated_at: 2023-04-23 15:54:11 UTC  
> Url: https://github.com/boostorg/geometry/pull/1142#discussion_r1174604232  

This is the fix proposed in #1139


---

## Review 2 [Commented]

> Username: barendgehrels  
> Created_at: 2023-04-23 15:54:55 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1142#pullrequestreview-1396930400  

📁 include/boost/geometry/algorithms/detail/sections/sectionalize.hpp

```diff
 909 |-     BOOST_STATIC_ASSERT((std::is_same<ctype1, ctype2>::value));
 910 |- 
 911 |- 
```

> Username: barendgehrels  
> Created_at: 2023-04-23 15:54:55 UTC  
> Url: https://github.com/boostorg/geometry/pull/1142#discussion_r1174604354  

This had to go, these types are not always the same.


---

## Comment 3

> Username: barendgehrels  
> Created_at: 2023-05-06 10:53:54 UTC  
> Url: https://github.com/boostorg/geometry/pull/1142#issuecomment-1537115614  

@awulkiew @vissarion OK to merge?

---

## Review 4 [Commented]

> Username: awulkiew  
> Created_at: 2023-05-07 13:20:33 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1142#pullrequestreview-1415908798  

📁 include/boost/geometry/algorithms/detail/overlay/append_no_dups_or_spikes.hpp

```diff
 106 |+         point_t rp;
 107 |+         geometry::detail::conversion::convert_point_to_point(p, rp);
 108 |+         traits::push_back<Range>::apply(r, rp);
```

> Username: awulkiew  
> Created_at: 2023-05-07 13:20:33 UTC  
> Url: https://github.com/boostorg/geometry/pull/1142#discussion_r1186852580  

The point could be moved here. In practice this wouldn't make any difference but if someone used e.g. Multiprecision coordinates we'd avoid copying.

> Username: barendgehrels  
> Created_at: 2023-05-08 17:50:56 UTC  
> Updated_at: 2023-05-08 17:50:57 UTC  
> Url: https://github.com/boostorg/geometry/pull/1142#discussion_r1187720393  

:heavy_check_mark:


---

## Review 5 [Approved]

> Username: awulkiew  
> Created_at: 2023-05-07 13:22:19 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/geometry/pull/1142#pullrequestreview-1415909049  

Yes, LGTM, thanks!

---

## Comment 6

> Username: vissarion  
> Created_at: 2023-05-07 13:26:35 UTC  
> Url: https://github.com/boostorg/geometry/pull/1142#issuecomment-1537442462  

Sorry for the delays, I can review it tomorrow.

---

## Review 7 [Approved]

> Username: vissarion  
> Created_at: 2023-05-08 08:50:21 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/geometry/pull/1142#pullrequestreview-1416387305  

Thanks, I am OK with merging, with few minor comments.

📁 include/boost/geometry/algorithms/detail/direction_code.hpp

```diff
 111 |+ 
 112 |+         // Declare unit type (equal for all types) and calc type (coerced to most precise)
 113 |+         using units_t = typename cs_angular_units<Point2>::type;
```

> Username: vissarion  
> Created_at: 2023-05-08 08:43:00 UTC  
> Updated_at: 2023-05-08 08:50:21 UTC  
> Url: https://github.com/boostorg/geometry/pull/1142#discussion_r1187189519  

This is `units_p_t` above. Should we reuse that type?

> Username: barendgehrels  
> Created_at: 2023-05-08 17:52:35 UTC  
> Url: https://github.com/boostorg/geometry/pull/1142#discussion_r1187721990  

:heavy_check_mark:

> Username: barendgehrels  
> Created_at: 2023-05-08 17:59:31 UTC  
> Url: https://github.com/boostorg/geometry/pull/1142#discussion_r1187729602  

Put it back, the one above was in a separate block (specifically for the assertions). I could move that too, but then it's actually clearer like it is now.

---

📁 include/boost/geometry/algorithms/detail/direction_code.hpp

```diff
 110 |+         using coor_p_t = typename coordinate_type<Point2>::type;
 111 |+ 
 112 |+         // Declare unit type (equal for all types) and calc type (coerced to most precise)
```

> Username: vissarion  
> Created_at: 2023-05-08 08:43:30 UTC  
> Updated_at: 2023-05-08 08:50:21 UTC  
> Url: https://github.com/boostorg/geometry/pull/1142#discussion_r1187190035  

What happens if unit type is not the same for all types? Should we assert here?

> Username: barendgehrels  
> Created_at: 2023-05-08 17:53:36 UTC  
> Url: https://github.com/boostorg/geometry/pull/1142#discussion_r1187723421  

The static assertion are above (line 98/102)

---

📁 include/boost/geometry/algorithms/detail/direction_code.hpp

```diff
 161 |+                             c0 :
 162 |+                             (std::min)(is_antilon1 ? c0 : math::abs(dlon1),
 163 |+                                     is_antilon2 ? c0 : math::abs(dlon2));
```

> Username: vissarion  
> Created_at: 2023-05-08 08:45:06 UTC  
> Updated_at: 2023-05-08 08:50:21 UTC  
> Url: https://github.com/boostorg/geometry/pull/1142#discussion_r1187191601  

should this be aligned with `is_antilon1` in above line?

> Username: barendgehrels  
> Created_at: 2023-05-08 17:55:18 UTC  
> Updated_at: 2023-05-08 17:55:19 UTC  
> Url: https://github.com/boostorg/geometry/pull/1142#discussion_r1187725901  

Indeed. :heavy_check_mark:   
Updated (I usually align in another way, changed to that)

---

📁 include/boost/geometry/algorithms/detail/direction_code.hpp

```diff
 210 |                             Point2 p)
 211 |     {
 212 |         typedef math::detail::constants_on_spheroid
```

> Username: vissarion  
> Created_at: 2023-05-08 08:46:00 UTC  
> Updated_at: 2023-05-08 08:50:21 UTC  
> Url: https://github.com/boostorg/geometry/pull/1142#discussion_r1187192497  

Since you replacing typedefs maybe also replace those?

> Username: barendgehrels  
> Created_at: 2023-05-08 17:56:20 UTC  
> Url: https://github.com/boostorg/geometry/pull/1142#discussion_r1187726799  

:heavy_check_mark: thanks


---
