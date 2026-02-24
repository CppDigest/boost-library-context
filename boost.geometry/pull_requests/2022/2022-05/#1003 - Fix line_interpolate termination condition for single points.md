# #1003 Fix line_interpolate termination condition for single points. [Merged]

> Username: tinko92  
> Created at: 2022-05-16 14:14:19 UTC  
> Updated at: 2022-05-24 16:42:54 UTC  
> Merged at: 2022-05-23 09:49:21 UTC  
> Closed at: 2022-05-23 09:49:21 UTC  
> Url: https://github.com/boostorg/geometry/pull/1003  

If I understood line_interpolate correctly, the early termination condition is intended to apply to all output geometry types that are single points rather than just the point type of the input range. I think this fixes #1000 .

---

## Review 1 [Approved]

> Username: vissarion  
> Created_at: 2022-05-17 11:00:22 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/geometry/pull/1003#pullrequestreview-975270812  

Great thanks!

---

## Review 2 [Commented]

> Username: awulkiew  
> Created_at: 2022-05-18 22:12:45 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1003#pullrequestreview-977706143  

📁 include/boost/geometry/algorithms/line_interpolate.hpp

```diff
 133 |                 Policy::apply(p, pointlike);
 133 |-                 if (std::is_same<PointLike, point_t>::value)
 134 |+                 if (std::is_same<typename tag<PointLike>::type, point_tag>::value)
```

> Username: awulkiew  
> Created_at: 2022-05-18 22:12:45 UTC  
> Updated_at: 2022-05-18 22:12:46 UTC  
> Url: https://github.com/boostorg/geometry/pull/1003#discussion_r876423301  

FYI, we have type traits for this now, i.e. `util::is_point<PointLike>::value` here:  
https://github.com/boostorg/geometry/blob/develop/include/boost/geometry/util/type_traits.hpp#L38  
  
Some compilers will complain about this `constant condition` but it was here before.

> Username: tinko92  
> Created_at: 2022-05-20 00:12:41 UTC  
> Url: https://github.com/boostorg/geometry/pull/1003#discussion_r877628826  

Thanks for the hint, I will update the PR tomorrow to use is_point.  
  
With regard to the constant condition: This was last solved with the BOOST_GEOMETRY_CONDITION macro, should that be used here as well?  
Alternatively, there is the BOOST_IF_CONSTEXPR macro from boost.config, which expands to "if" for <=c++14 and "if constexpr" for >c++17 (which was apparently available in the compiler that generated the warning: https://github.com/boostorg/geometry/issues/727)


---

## Review 3 [Approved]

> Username: barendgehrels  
> Created_at: 2022-05-22 07:12:51 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/geometry/pull/1003#pullrequestreview-980908577  

Thanks!

---
